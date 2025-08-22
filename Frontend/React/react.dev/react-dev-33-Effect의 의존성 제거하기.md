# React.dev-docs-33-Effect의 의존성 제거하기

[REACT 학습하기 > 탈출구 > Effect의 의존성 제거하기](https://ko.react.dev/learn/removing-effect-dependencies)

## Goal

Effect를 작성하면 린터는 Effect의 의존성 목록에 Effect가 읽는 모든 **반응형 값**(예: props, state, context 등)이 포함되었는지 확인한다.

이는 Effect가 항상 **최신 상태와 동기화**되도록 보장하기 위함이다.

그러나 불필요한 의존성은 Effect를 과도하게 재실행시키거나 심지어 **무한 루프**를 유발할 수 있다.

이번 학습에서는 **의존성을 코드와 일치**시키는 원칙 아래, **불필요한 의존성을 제거**하는 구체적 방법을 정리한다.

- Effect 의존성 무한 루프를 수정하는 방법을 설명한다.
- 의존성을 제거하려면 무엇을 바꿔야 하는지 제시한다.
- Effect에 “반응”하지 않고 최신 값을 읽는 방법을 제안한다.
- 객체·함수 의존성을 피하는 구조화 기법을 소개한다.
- 의존성 린터를 억제하지 않고 해결하는 원칙을 제시한다.

## 의존성은 코드와 일치해야 합니다.

- 의존성 배열은 **Effect 본문에서 실제로 읽은 반응형 값**과 **정확히 일치**해야 한다.
- “의존성이 마음에 들지 않는다”면 배열을 바꾸는 게 아니라 **코드를 바꿔서** 의존성이 자연스럽게 줄도록 해야 한다.

### 의존성을 제거하려면 의존성이 아님을 증명하세요

- Effect가 어떤 값을 더 이상 **읽지 않도록** 코드를 바꾸면, 그 값은 의존성에서 제거될 수 있다.
- 예: 최신 핸들러 로직만 쓰고 **재구독은 원치 않을 때** → `useEvent`(또는 ref 동기화)로 **비반응형 함수**를 만들어 Effect에서 호출한다.

```tsx
import { experimental_useEffectEvent as useEvent } from 'react';

function Chat({ roomId, onMessage }: { roomId: string; onMessage: (m: string) => void }) {
  const handle = useEvent(onMessage); // 최신 로직, 비반응형
  useEffect(() => {
    const conn = createConnection(roomId);
    conn.connect();
    conn.on('message', (m) => handle(m)); // 재구독 없이 최신 핸들러
    return () => conn.disconnect();
  }, [roomId]); // ✅ onMessage 제거 가능
}
```

### 의존성을 변경하려면 코드를 변경하세요.

- **객체/함수 식별자**가 매 렌더 바뀐다면 `useMemo`/`useCallback`으로 **안정화**하거나, **Effect 내부로 이동**해 의존성을 피한다.
- **파생 값**은 렌더 중 계산으로 돌리고, Effect에는 **동기화만** 남긴다.

> **의존성 린터를 억제하는 것이 왜 위험한가요?**
>
> 린터 억제는 **stale 클로저**·**중복 구독**·**환경/모드별 오동작**(Strict Mode, 대기복원, 전환)으로 이어진다. 경고를 무시하기보다 **설계를 재구성**해 경고가 해소되도록 한다. 억제는 외부의 **진짜 불변 상수** 같은 예외적 상황에 한정한다.

## 불필요한 의존성 제거하기

### 이 코드를 이벤트 핸들러로 옮겨야 하나요?

- 네트워크 **쓰기(POST/DELETE)**, 알림 띄우기 등 **상호작용의 직접 결과**는 **이벤트 핸들러**로 옮긴다. Effect에 두면 렌더 재시도로 **중복 실행**될 수 있다.

```tsx
// ❌ Effect에서 전송(재실행 위험)
useEffect(() => {
  submit(formData);
}, [formData]);

// ✅ 클릭 이벤트에서 전송
const onSubmit = () => submit(formData);
```

### Effect 가 관련 없는 여러 가지 작업을 수행하나요?

- 한 Effect에 **서로 다른 이유**로 실행되는 로직이 섞여 있으면 의존성이 **합집합**으로 불어나 재실행이 많아진다. **Effect를 분할**한다.

```tsx
// ❌ 구독 + document.title 갱신이 섞임
useEffect(() => {
  subscribe(user.id);
  document.title = page;
}, [user, page]);

// ✅ 관심사 분리
useEffect(() => subscribe(user.id), [user.id]);
useEffect(() => {
  document.title = page;
}, [page]);
```

### 다음 State를 계산하기 위해 어떤 State를 읽고 있나요?

- 다른 state를 **읽고** 다음 state를 계산하려 들면 읽은 state를 의존성에 추가해야 하므로 루프가 생길 수 있다.
- **업데이터 함수**(함수형 업데이트)로 해결한다.

```tsx
// ❌ count를 읽어 갱신 → 의존성 필요
useEffect(() => {
  setCount(count + 1);
}, [count]);

// ✅ 함수형 업데이트로 의존성 제거
useEffect(() => {
  const id = setInterval(() => setCount((c) => c + 1), 1000);
  return () => clearInterval(id);
}, []);
```

### 값의 변경에 ‘반응’하지 않고 값을 읽고 싶으신가요?

- **최신 값 접근**은 필요하지만 **재실행**은 원치 않을 때가 있다.

#### props를 이벤트 핸들러로 감싸기

- 최신 `props`를 사용하는 로직을 **이벤트로 감싸** Effect 밖으로 이동한다.

```tsx
function Save({ data }: { data: Draft }) {
  const onClick = () => saveDraft(data); // 최신 data는 호출 시점에 읽힘
  return <button onClick={onClick}>저장</button>;
}
```

#### 반응형 코드와 비반응형 코드 분리

- 최신 로직은 **Effect 이벤트**(또는 ref 동기화)로 분리하고, Effect는 **최소 의존성**만 가진다.

```tsx
const onTick = useEvent(() => {
  // 최신 state/props 사용 가능
  analytics.heartbeat(user.id);
});
useEffect(() => {
  const id = setInterval(() => onTick(), 30000);
  return () => clearInterval(id);
}, []); // ✅ 재설치 없음
```

### 일부 반응형 값이 의도치 않게 변경되나요?

- 렌더마다 새 객체/함수 생성으로 **불필요한 변경**이 발생할 수 있다.

#### 정적 객체와 함수를 컴포넌트 외부로 이동

```tsx
// ✅ 변하지 않는 상수/헬퍼는 모듈 스코프
const STATIC_HEADERS = { Accept: 'application/json' };
function buildURL(id: string) {
  return `/api/users/${id}`;
}
```

#### Effect 내에서 동적 객체 및 함수 이동

- 의존성으로 포함하지 않을 객체는 **Effect 내부에서 생성**한다.

```tsx
useEffect(() => {
  const options = { headers: { Authorization: token } }; // 내부에서 생성
  fetch(url, options);
}, [url, token]); // options 의존성 불필요
```

#### 객체에서 원시 값 읽기

- 객체 전체 대신 **필요한 원시 값**만 의존성으로 사용한다.

```tsx
// ❌ user 객체 의존
useEffect(() => sync(user), [user]);

// ✅ 필요한 키만
useEffect(() => sync({ id: user.id }), [user.id]);
```

#### 함수에서 원시값 계산

- 의존성으로 함수를 넣지 말고 **필요한 결과**를 계산해 **원시 값**을 의존성으로 둔다.

```tsx
// ❌ getUrl 의존
useEffect(() => {
  fetch(getUrl());
}, [getUrl]);

// ✅ url을 계산해 의존
const url = useMemo(() => getUrl(base, q), [base, q]);
useEffect(() => {
  fetch(url);
}, [url]);
```

## 요약

- 의존성은 항상 **코드와 일치**해야 한다.
- 의존성이 마음에 들지 않으면 **코드를 수정**해야 한다.
- 린터 억제는 혼란스러운 버그를 만들므로 **피한다**.
- 의존성을 제거하려면 해당 값이 **더 이상 반응형으로 읽히지 않음**을 **코드로 증명**해야 한다.
- 상호작용 결과 코드는 **이벤트 핸들러**로 이동한다.
- 서로 다른 이유의 동작은 **Effect를 분할**한다.
- 이전 state 기반 갱신은 **업데이터 함수**를 사용한다.
- “반응”하지 않고 최신 값을 읽으려면 **Effect 이벤트**(또는 ref 동기화)를 사용한다.
- 객체/함수 의존성은 쉽게 달라지므로 피하고, **컴포넌트 외부** 또는 **Effect 내부**로 이동한다.

## 인사이트

- “의존성 줄이기”의 정답은 배열 편집이 아니라 **데이터 흐름 재설계**이다.
- **반응형(Effect)** 과 **비반응형(이벤트/Effect 이벤트)** 을 분리하면 재실행을 통제하기 쉽다.
- 객체 전체 의존성 대신 **원시 키**를 의존성으로 삼으면 예측 가능성이 높아진다.
- 최신성, 안정성, 캡슐화의 균형을 위해 `useEvent`/ref, `useMemo`/`useCallback`, Effect 분할을 조합하여 사용한다.
