# React.dev-docs-32-Effect에서 이벤트 분리하기

[REACT 학습하기 > 탈출구 > Effect에서 이벤트 분리하기](https://ko.react.dev/learn/separating-events-from-effects)

## Goal

이벤트 핸들러는 동일한 상호작용이 반복될 때만 실행된다.

반면 Effect는 읽은 **반응형 값** (props, state, context 등)이 지난 렌더와 달라지면 **동기화가 필요**하다고 판단되어 실행된다.

때로는 한 Effect 안에서 **일부 로직은 반응형이 아니길** 바라거나, **최근 props/state를 읽되 재구독은 피하고** 싶은 상황이 있다.

이 문서에서는 이벤트와 Effect의 역할을 분리하고, **Effect 이벤트(useEvent)** 로 비반응형 로직을 추출하는 방법을 정리한다.

- 이벤트 핸들러와 Effect 중에서 선택하는 기준을 정리한다.
- Effect가 반응형이고 이벤트 핸들러는 비반응형인 이유를 이해한다.
- Effect 내부의 비반응형 로직을 **Effect 이벤트**로 분리하는 법을 학습한다.
- Effect 이벤트로 **최근 props/state**를 읽으면서 **재구독을 피하는** 패턴을 익힌다.

## 이벤트 핸들러와 Effect 중에 선택하기

### 이벤트 핸들러는 특정 상호작용에 대한 응답으로 실행된다

- 버튼 클릭, 입력 변경, 폼 제출 등 **사용자 상호작용**에 대한 코드는 이벤트 핸들러에서 처리한다.
- 네트워크 **쓰기(POST/DELETE)**, 결제 요청, 토스트 띄우기 등 **상호작용의 직접 결과**는 이벤트에서 실행한다.

```tsx
function BuyButton({ sku }: { sku: string }) {
  const onClick = async () => {
    await purchase(sku); // 이벤트에서 실행
    toast('구매 완료');
  };
  return <button onClick={onClick}>구매하기</button>;
}
```

### Effect는 동기화가 필요할 때마다 실행된다

- 구독 설치/해제, DOM 측정·제어, 타이머·애니메이션, 네트워크 **읽기** 등 **외부 시스템과의 동기화**는 Effect에서 수행한다.
- 의존성이 변하면 **이전 동기화 정리 → 새 동기화 설치** 순서로 재실행된다.

```tsx
function ChatRoom({ roomId, onMessage }: { roomId: string; onMessage: (m: string) => void }) {
  useEffect(() => {
    const conn = createConnection(roomId);
    conn.connect();
    conn.on('message', onMessage);
    return () => {
      conn.off('message', onMessage);
      conn.disconnect();
    };
  }, [roomId, onMessage]); // 의존성 정확히 지정
  return null;
}
```

## 반응형 값과 반응형 로직

### 이벤트 핸들러 내부의 로직은 반응형이 아니다

- 이벤트 핸들러는 **호출 순간**의 값을 읽고 동작한다. 다음 렌더로 자동 재호출되지 않는다.
- 따라서 핸들러 내부 로직은 **의존성 배열** 개념이 없으며, “최신 값”이 필요하면 **현재 호출 시점에** 읽으면 된다.

### Effect 내부의 로직은 반응형이다

- Effect는 **읽은 반응형 값**이 바뀌면 다시 실행된다. 의존성에 따라 재구독/재설치가 발생한다.
- Effect가 너무 많은 값에 의존하면 **불필요한 재실행**과 **깜빡임**이 생길 수 있으므로, 반응해야 하는 값만 의존성에 포함하도록 설계한다.

## Effect에서 비반응형 로직 추출하기

- 한 Effect 안에 “재구독은 원치 않지만 최신 로직은 실행하고 싶은” 코드가 섞여 있다면, 해당 부분을 **Effect 이벤트**로 추출한다.

### Effect 이벤트 선언하기

- React 19: `useEffectEvent`, React 18: `experimental_useEffectEvent` 를 사용한다.
- Effect 이벤트는 **안정 식별자**를 가지며, 호출 시점의 **최신 props/state**를 읽는다. 단, **반응형이 아니다**(이벤트처럼 동작).

```tsx
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEvent } from 'react'; // React 19면 useEvent

function Room({ roomId, onReceive }: { roomId: string; onReceive: (m: string) => void }) {
  const handleMessage = useEvent((m: string) => {
    // 최신 props/state 사용 가능, 하지만 이 함수 자체는 비반응형
    onReceive(m);
  });

  useEffect(() => {
    const conn = createConnection(roomId);
    conn.connect();
    conn.on('message', (m) => handleMessage(m)); // 재구독 없이 최신 로직
    return () => conn.disconnect();
  }, [roomId]); // ✅ onReceive를 의존성에서 제거 가능
  return null;
}
```

### Effect 이벤트로 최근 props와 state 읽기

- Effect 이벤트 내부에서는 **최신 props/state**를 읽을 수 있다. 이 덕분에 Effect는 **최소 의존성**만 유지하고, 로직은 최신으로 실행한다.

```tsx
function PageTracker({ url }: { url: string }) {
  const send = useEvent(() => {
    analytics.track('visit', { url }); // 항상 최신 url
  });

  useEffect(() => {
    // url 변화에 맞춰 한 번만 보내고 싶다면 여기서 호출
    send();
  }, [url]);

  return null;
}
```

> **중요합니다!**
>
> “인수 없이 `onVisit()`을 호출하고 내부에서 `url`을 읽을 수 있는가?”에 대한 답은 **예**이다. **Effect 이벤트는 항상 최신 props/state를 본다.** 다만 이 함수 자체는 비반응형이므로, **언제 호출할지**는 Effect나 이벤트에서 **명시적으로 제어**해야 한다.

> **대안으로 의존성 린터를 억제하는 것은 괜찮은가요?**
>
> 일반적으로 **권장하지 않는다.** 린터 경고를 무시하기보다 **Effect 이벤트로 비반응형 부분을 분리**하거나, **의존성이 필요한 설계로 재구성**하는 편이 안전하다. 억제는 외부 불변 상수 등 **명백한 예외**에 한정한다.

### Effect 이벤트의 한계

- **반드시 Effect 내부에서만 호출**해야 한다. 다른 컴포넌트/훅으로 **전달하거나 저장(ref/상태)에 보관**해서는 안 된다.
- **비반응형**이므로 의존성 변화에 따라 자동으로 다시 실행되지 않는다. 언제 실행할지는 호출부에서 관리한다.
- DOM 이벤트 핸들러로 **직접 등록하지 않는다.** DOM 이벤트는 일반 이벤트 핸들러를 사용한다.

> **주의하세요!**
>
> Effect 이벤트를 **children에 prop으로 넘기거나** 일반 이벤트 핸들러로 사용하면 안 된다. Effect 이벤트는 **현재 컴포넌트의 Effect 컨텍스트**에서만 의미가 있으며, 외부로 노출하면 생명주기/동기화 규칙이 깨진다.

## 요약

- 이벤트 핸들러는 특정 상호작용에 대한 응답으로 실행된다.
- Effect는 동기화가 필요할 때마다 실행된다.
- 이벤트 핸들러 내부 로직은 비반응형이다.
- Effect 내부 로직은 반응형이다.
- Effect의 비반응형 로직은 **Effect 이벤트**로 옮길 수 있다.
- Effect 이벤트는 Effect 내부에서만 호출한다.
- Effect 이벤트를 다른 컴포넌트나 훅에 전달하지 않는다.

## 인사이트

- `useEffectEvent`를 아직 실험 중인 기능이라 칭하면서도, 공식 문서 튜토리얼의 상당 부분을 차지하도록 설명해둔 것이 인상적이다.
  - 해당 기능이 실무에서도 많이 사용될 것 같다.
  - ref 사용과의 이점을 비교해보면 좋을 것 같다.
- “**동기화(구독/측정/네트워크 읽기)** 는 Effect, **행위(클릭/제출/쓰기)** 는 이벤트”라는 경계를 지키면 설계가 단순해진다.
- Effect 이벤트는 **최신성(최근 props/state)** 과 **안정성(재구독 최소화)** 을 동시에 만족시키는 실용적 도구이다.
- 린터 경고는 **설계의 실수**일 가능성이 크다. 억제보다 **Effect 이벤트/의존성 분해/핸들러 이동**으로 구조를 개선한다.
- 의존성을 줄이려고 모든 로직을 이벤트로 옮기면 **동기화 누락**이 생길 수 있다. 반대로 모든 것을 Effect에 몰면 **과잉 재실행**이 발생한다. 두 축의 균형을 유지한다.
