# React.dev-docs-34-커스텀 Hook으로 로직 재사용하기

[REACT 학습하기 > 탈출구 > 커스텀 Hook으로 로직 재사용하기](https://ko.react.dev/learn/reusing-logic-with-custom-hooks)

## Goal

React에는 `useState`, `useContext`, `useEffect`와 같은 내장 Hook이 존재한다.

실제 애플리케이션에서는 **도메인에 특화된 로직**이 추가로 필요하며, 이 로직을 여러 컴포넌트에서 반복하지 않기 위해 **커스텀 Hook**을 만든다.

커스텀 Hook은 UI를 렌더링하지 않고 **상태 관리, 동기화, 구독, 계산** 같은 비주얼이 아닌 로직을 캡슐화한다.

- 커스텀 Hook의 개념과 작성 방법을 이해한다.
- 컴포넌트 간 로직을 재사용하는 패턴을 습득한다.
- 커스텀 Hook의 이름 규칙과 구조를 정리한다.
- 언제, 왜 Hook을 추출해야 하는지 기준을 수립한다.

---

## 커스텀 Hook: 컴포넌트간 로직 공유하기

- 커스텀 Hook은 **함수**이며, 내부에서 다른 Hook을 호출해 **상태와 이펙트 로직을 조합**한다.
- 커스텀 Hook은 **UI를 직접 반환하지 않고 값/함수**를 반환한다. UI는 이 Hook을 사용하는 컴포넌트가 담당한다.

```tsx
// 예: 온라인 상태 추적
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return <h1>{isOnline ? '✅ 온라인' : '❌ 연결 안 됨'}</h1>;
}
```

### 컴포넌트로부터 커스텀 Hook 추출하기

1. **반복되는 관심사**를 찾는다(예: 구독 설치/해제, 폼 검증, 타이머).
2. 관련 **state / ref / effect**를 하나의 함수로 모은다.
3. 컴포넌트가 필요로 하는 **최소한의 API**(값·업데이터·핸들러)를 반환한다.
4. 컴포넌트에서는 Hook을 호출하여 **로직을 위임**한다.

이로써 두 컴포넌트를 단순화할 수 있고, 둘 간의 중복을 제거할 수 있게 된다.

```tsx
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ 온라인' : '❌ 연결 안 됨'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ 진행사항 저장됨');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? '진행사항 저장' : '재연결 중...'}
    </button>
  );
}

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

### Hook의 이름은 항상 use로 시작해야 합니다.

- `useXxx` 네이밍은 **린터가 호출 규칙을 검증**하고, 코드 독자에게 “이 함수는 Hook”임을 알리기 위한 **계약**이다.
  - 이를 어길 시 linter 오류 발생

> **렌더링 중에 호출되는 모든 함수는 use 접두사로 시작해야 하나요?**
>
> 그렇지 않다. **Hook을 호출하는 함수만** `use` 접두사를 갖는다. 렌더 중 호출되더라도 내부에서 Hook을 **호출하지 않는** 순수 유틸 함수는 `use`로 시작하면 안 된다. 반대로 Hook을 호출한다면 렌더 조건, 루프, 분기 없이 **최상위에서** 호출되어야 한다.

### 커스텀 Hook은 state 그 자체를 공유하는게 아닌 state 저장 로직을 공유하도록 합니다.

- 커스텀 Hook은 **싱글턴이 아니다.** Hook을 호출하는 **각 컴포넌트 인스턴스마다 독립적인 state**가 생성된다. 따라서 “상태 공유”가 아니라 **_상태를 다루는 방법(로직) 공유_** 이다.
- 인스턴스 간에 실제 데이터를 공유하려면 **상위 state 끌어올리기**나 **Context/외부 스토어**를 사용한다.

---

## Hook 사이에 상호작용하는 값 전달하기

- Hook은 **매 렌더** 호출되며, 인자로 전달된 값은 해당 렌더의 **스냅샷**이다.
- 서로 다른 Hook 간에는 **원시 값**(id, key) 또는 **안정화된 함수**를 전달하여 의존성 폭발을 방지한다.

### 커스텀 Hook에 이벤트 핸들러 넘겨주기

- Hook 내부에서 구독/타이머를 설치하며 **최신 핸들러**를 호출해야 할 때는 **`useEvent`(또는 ref 동기화)** 를 사용한다. 이렇게 하면 의존성에 핸들러를 넣지 않고도 **재구독 없이 최신 로직**을 실행한다.

```tsx
import { experimental_useEffectEvent as useEvent } from 'react';

export function useInterval(onTick: () => void, ms: number | null) {
  const handle = useEvent(onTick); // 최신 콜백
  useEffect(() => {
    if (ms == null) return;
    const id = setInterval(() => handle(), ms);
    return () => clearInterval(id);
  }, [ms]); // 최소 의존성
}
```

### 언제 커스텀 Hook을 사용해야 하는지

- **여러 컴포넌트에서 동일 로직**을 반복할 때.
- 한 컴포넌트의 파일에 **state/effect 로직이 비대**해졌을 때.
- **도메인 규칙**(포커스 관리, 접근성, 폼 검증)을 캡슐화하여 재사용하고 싶을 때.
- 팀 공통 라이브러리로 **내부 표준화**를 원할 때.

> **커스텀 Hook이 구체적인 고급 사용 사례에 집중하도록 하기**  
> Hook은 “만능”이 아니라 **명확한 목적**을 가져야 한다. 옵션은 **필요 최소한**으로 유지하고, 반환 API는 **좁고 일관적**이어야 한다. 과도한 설정 가능성은 **의존성과 복잡도**를 키운다.

### 커스텀 Hook은 더 나은 패턴으로 변경할 수 있도록 도와줍니다.

- 초기에 Effect 기반으로 구현했다가, 나중에 **외부 스토어/데이터 라이브러리**로 이관하더라도 **컴포넌트 변경을 최소화**할 수 있다. 내부 구현을 교체하되 **반환 API 계약**은 유지한다.

> **React가 데이터 패칭을 위한 내부 해결책을 제공할까요?**
>
> `use` 페칭 로직을 작업 중  
> `const cities = use(fetch(`/api/cities?country=${country}`));`
>
> 현재는 React 자체는 **데이터 패칭 및 캐싱의 표준 구현**을 내장하지 않는다. 프레임워크(예: Next.js 서버 로딩/서버 컴포넌트)나 라이브러리(예: React Query, SWR)를 사용해 **캐싱, 중복 제거, 동기화**를 맡기는 것이 바람직하다. 커스텀 Hook은 이러한 도구 위에 **도메인별 래퍼**를 제공할 수 있다.

### 여러 방법이 존재합니다.

- **반환 형태**는 상황에 맞춰 결정한다: 튜플 `[state, actions]`, 객체 `{ data, refresh }` 등.
- **네이밍**은 의미 중심으로: `useForm`, `useAuth`, `useInfiniteList` 등.
- **구성 가능성**을 고려해 작은 Hook을 **합성**한다(`useA` + `useB` → `useAB`).

---

## 요약

- 커스텀 Hook을 사용하면 컴포넌트 간 로직을 공유할 수 있다.
- 커스텀 Hook의 이름은 `use` 뒤에 대문자로 시작해야 한다.
- 커스텀 Hook은 **state 자체가 아닌, state 저장 로직**을 공유한다.
- 한 Hook에서 다른 Hook으로 **반응형 값**을 전달할 수 있으며, 값은 최신 상태로 유지된다.
- 모든 Hook은 컴포넌트가 재렌더링될 때마다 재실행된다.
- 커스텀 Hook의 코드는 컴포넌트 코드처럼 **순수**해야 한다(렌더 중 부수효과 금지).
- 커스텀 Hook으로 전달받은 **이벤트 핸들러**를 구독에서 호출한다면 **Effect로 감싸고 `useEvent`** 로 최신성을 보장한다.
- `useMount` 같은 모호한 Hook은 만들지 않는다. **목적을 명확**히 한다.
- 경계(어디까지 추출할지)는 팀과 코드베이스 맥락에 맞춰 **실용적으로** 결정한다.

---

## 인사이트

- 커스텀 Hook은 **재사용 가능한 도메인 모듈**이다. “UI는 컴포넌트, 로직은 Hook”이라는 분업을 유지하면 테스트·교체·확장이 쉬워진다.
- **최신성 vs 안정성** 문제는 `useEvent`/ref 동기화, `useMemo`/`useCallback`, **의존성 최소화**로 해결한다.
- Hook API는 **작고, 예측 가능하며, 선언적**이어야 한다. 외부 계약을 안정적으로 유지하면 내부 구현은 자유롭게 진화할 수 있다.
