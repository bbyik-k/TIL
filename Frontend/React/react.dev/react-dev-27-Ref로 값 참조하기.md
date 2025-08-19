# React.dev-docs-27-Ref로 값 참조하기

[REACT 학습하기 > 탈출구 > Ref로 값 참조하기](https://ko.react.dev/learn/referencing-values-with-refs)

## Goal

컴포넌트가 일부 정보를 “기억”하고자 하나 해당 정보가 **렌더링을 유발하지 않도록** 유지하려면 Ref를 사용한다.

- 컴포넌트에 Ref를 추가하는 방법을 이해한다.
- Ref의 값이 어떻게 업데이트되는지 파악한다.
- Ref와 State의 차이를 비교한다.
- Ref를 안전하고 적절하게 사용하는 시점을 정리한다.

## 컴포넌트에 Ref를 추가하기

- `useRef(initialValue)`는 `{ current: initialValue }` 형태의 **가변 컨테이너**를 반환한다. 이 컨테이너는 **렌더 사이에 유지**된다.

- `ref.current`를 변경해도 **_리렌더링을 트리거하지 않는다_**. 따라서 **UI에 직접 표시되지 않는 정보**(타이머 ID, 외부 라이브러리 인스턴스, 최신 값 등)를 저장하기에 적합하다.

```tsx
import { useRef } from 'react';

export default function SearchBox() {
  const latestQueryRef = useRef('');
  const timeoutIdRef = useRef<number | null>(null);

  function onChange(e: React.ChangeEvent<HTMLInputElement>) {
    latestQueryRef.current = e.target.value; // 리렌더 없이 최신값만 보관
  }

  function trigger() {
    if (timeoutIdRef.current !== null) clearTimeout(timeoutIdRef.current);
    timeoutIdRef.current = window.setTimeout(() => {
      console.log('search:', latestQueryRef.current);
    }, 300);
  }

  return <input onChange={onChange} onKeyUp={trigger} />;
}
```

> **주의하세요!**  
> `ref.current`를 **렌더 중에 읽거나 쓰지 않는다.** 렌더는 순수해야 하며, 렌더 중 Ref 변경은 컴포넌트를 예측하기 어렵게 만든다. Ref 접근·수정은 **이벤트 핸들러**나 **이펙트**에서 수행한다.

## 예시: 스톱워치 작성하기

리렌더 없이 측정 데이터를 누적하려면 **측정용 값**(시작 시각, 인터벌 ID)을 Ref에 두고, **표시에 필요한 값**만 `state`로 반영한다.

```tsx
import { useEffect, useRef, useState } from 'react';

export default function Stopwatch() {
  const [elapsed, setElapsed] = useState(0); // 화면 표시(ms)
  const startRef = useRef<number | null>(null); // 시작 시각
  const tickIdRef = useRef<number | null>(null); // interval ID

  const start = () => {
    if (tickIdRef.current != null) return;
    startRef.current = Date.now() - elapsed; // 일시정지 후 재시작 고려
    tickIdRef.current = window.setInterval(() => {
      setElapsed(Date.now() - (startRef.current ?? Date.now()));
    }, 50);
  };

  const stop = () => {
    if (tickIdRef.current != null) {
      clearInterval(tickIdRef.current);
      tickIdRef.current = null;
    }
  };

  const reset = () => {
    stop();
    startRef.current = null;
    setElapsed(0);
  };

  useEffect(() => () => stop(), []); // 언마운트 시 정리

  return (
    <>
      <h1>{(elapsed / 1000).toFixed(2)}s</h1>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
      <button onClick={reset}>Reset</button>
    </>
  );
}
```

> **주의하세요!**  
> 인터벌·타임아웃, 애니메이션 등 **외부 작업의 식별자**(ID)는 Ref에 보관한다. 이를 `state`로 두면 매 틱마다 리렌더를 유발하거나, 의존성에 따라 **불필요한 재설치**가 발생할 수 있다.

## Ref와 State의 차이

| 항목        | Ref                                                         | State                                 |
| ----------- | ----------------------------------------------------------- | ------------------------------------- |
| 저장 형태   | `{ current }`의 **가변 컨테이너**                           | React가 관리하는 **반응형 스냅샷**    |
| 렌더 트리거 | `ref.current` 변경 **X**                                    | `setState` 호출 시 **O**              |
| 주 용도     | 타이머 ID, 외부 객체, 최신 값 저장 등 **부수효과용 데이터** | UI 계산·표시에 필요한 **표시 데이터** |
| 접근 시점   | 이벤트 핸들러/이펙트에서 읽기/쓰기                          | 렌더 중 자유롭게 읽어서 UI 계산       |
| 동일성      | `useRef`는 매 렌더 **동일 객체** 반환                       | 값 변경 시 리렌더로 **새 값** 제공    |

> **주의하세요!**  
> UI에 반영되어야 하는 데이터까지 Ref로 숨기지 않는다. Ref는 **반응성이 없으므로**, `ref.current`를 바꿔도 화면이 갱신되지 않는다. UI 반영이 필요하면 `state`로 승격한다.

## Ref를 사용할 시기

- **타이머/인터벌/애니메이션 ID** 보관 및 정리 시 사용한다.
- **외부 라이브러리 인스턴스**(비디오 플레이어, 차트, 맵 등)를 보관한다.
- **최신 props/state 스냅샷**을 보관하여 **Stale Closure**를 방지한다.
- **자주 바뀌지만 렌더와 무관한 플래그·카운터**를 추적한다.
- **재생성 비용이 큰 객체**를 리렌더 간 재사용한다.

> **주의하세요!**  
> 단순히 “여러 컴포넌트에서 공유”해야 한다고 Ref를 택하지 않는다. 그 경우는 보통 **상위 state, context, 외부 스토어**가 더 적절하다.

## Ref의 좋은 예시

- **디바운스/스로틀**: 직전 타임아웃 ID를 Ref에 보관하여 취소, 재예약한다.
- **최신 이벤트 핸들러 동기화**: `handlerRef.current = latestHandler`로 재구독 없이 최신 로직을 실행한다.
- **이전 값 보관**: `prevRef.current = value`로 변화 감지를 수행한다.
- **비동기 취소 플래그**: 언마운트 후 콜백 무시용 플래그를 Ref로 관리한다.

```tsx
// 재구독 없이 최신 로직을 실행하는 인터벌 훅
import { useEffect, useRef } from 'react';

export function useInterval(onTick: () => void, delay: number | null) {
  const fnRef = useRef(onTick);
  fnRef.current = onTick; // 렌더마다 최신화

  useEffect(() => {
    if (delay == null) return;
    const id = setInterval(() => fnRef.current(), delay);
    return () => clearInterval(id);
  }, [delay]); // 구독은 delay에만 의존
}
```

## Ref와 DOM

- DOM 제어가 필요할 때 **DOM Ref**를 사용한다: `const inputRef = useRef<HTMLInputElement>(null)` → `<input ref={inputRef} />`.
- 포커스·스크롤·선택 영역 등은 **이벤트 핸들러/이펙트**에서 수행한다.
- 레이아웃 측정이나 페인트에 민감한 조작은 **`useLayoutEffect`**에서 수행한다.

```tsx
function Modal() {
  const panelRef = useRef<HTMLDivElement>(null);
  useEffect(() => {
    panelRef.current?.focus(); // 마운트 후 포커스
  }, []);
  return <div ref={panelRef} tabIndex={-1} role='dialog' />;
}
```

> **주의하세요!**  
> Ref를 **조건부로 다른 노드에 재사용**하면 이전 참조가 남을 수 있다. 필요 시 `key`나 분기된 Ref를 사용해 의도치 않은 DOM 참조 누수를 방지한다.

## 요약

- Ref는 렌더링에 사용되지 않는 값을 고정하기 위한 **탈출구**이며, 남용하지 않는다.
- Ref는 `current` 프로퍼티를 가진 **순수 객체**이며, `useRef`로 생성한다.
- Ref와 State는 모두 렌더 사이 정보를 유지한다. 단, **Ref 변경은 리렌더를 유발하지 않는다.**
- 렌더 중 `ref.current`를 **읽거나 쓰지 않는다.**
- 타이머 ID·외부 인스턴스·최신 값 등 **부수효과용 데이터**는 Ref에 둔다.
- UI에 반영될 데이터는 **State**로 관리한다.

## 인사이트

- Ref는 **컴포넌트 인스턴스의 로컬 변수**로 이해한다. “UI 계산은 state, 부수효과는 ref”라는 경계를 지키면 코드가 예측 가능해진다.
- **Stale Closure**를 피하려면 구독은 `delay` 등 **최소 의존성만** 갖고, 최신 로직은 **`ref.current` 또는 `useEvent`** 로 참조한다.
- 외부 스토어 구독에는 **`useSyncExternalStore`** 를 사용하여 SSR/Concurrent 환경에서 일관성을 보장한다.
- 성능상 불필요한 리렌더를 피하려면 값 자체를 Ref에 두고, **표시가 필요할 때만** `state`로 승격하는 전략을 적용한다.
