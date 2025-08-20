# React.dev-docs-30-Effect가 필요하지 않은 경우

[REACT 학습하기 > 탈출구 > Effect가 필요하지 않은 경우](https://ko.react.dev/learn/you-might-not-need-an-effect)

## Goal

Effect는 React 패러다임에서 벗어나 외부 시스템(브라우저 DOM, 네트워크, 타사 위젯 등)과 동기화할 때 사용하는 **탈출구**이다.  
외부 시스템이 관여하지 않는다면 대부분의 경우 Effect가 필요하지 않다.  
불필요한 Effect를 제거하면 코드가 단순해지고, 성능과 신뢰성이 향상된다.

- 컴포넌트에서 불필요한 Effect를 제거하는 이유와 방법을 정리한다.
- Effect 없이 값비싼 계산을 캐시하는 방법을 정리한다.
- Effect 없이 컴포넌트 state를 초기화/조정하는 방법을 정리한다.
- 이벤트 핸들러 간에 로직을 공유하는 방법을 정리한다.
- 이벤트 핸들러로 이동해야 하는 로직을 구분한다.
- 부모 컴포넌트에 변경 사항을 알리는 방법을 정리한다.

## 불필요한 Effect를 제거하는 방법

Effect가 필요하지 않은 두 가지 일반적인 경우

1. 렌더링을 위해 데이터를 변환하는 데 Effect가 필요하지 않다.
2. 사용자 이벤트를 처리하는 데 Effect가 필요하지 않다.

### props 또는 state에 따라 state 업데이트하기

- **파생 값**은 Effect로 동기화하지 않고 **렌더 중 계산**한다. 이는 React의 선언적 모델에 부합한다.
- 기존 값으로부터 새 값을 “복사”하여 state에 유지하려는 패턴은 **중복 소스**를 만든다. 가능하면 **단일 소스(파생 계산)** 로 유지한다.

```tsx
// ❌ 나쁜 예: props를 state로 복사 후 Effect로 동기화
function Price({ amount, taxRate }: { amount: number; taxRate: number }) {
  const [total, setTotal] = useState(amount * (1 + taxRate));
  useEffect(() => setTotal(amount * (1 + taxRate)), [amount, taxRate]);
  return <div>{total}</div>;
}

// ✅ 좋은 예: 렌더 중 파생
function Price({ amount, taxRate }: { amount: number; taxRate: number }) {
  const total = amount * (1 + taxRate);
  return <div>{total}</div>;
}

//추가 예시
// ✅ 좋습니다: 렌더링 중에 계산됨
const fullName = firstName + ' ' + lastName;
```

### 비용이 많이 드는 계산 캐싱하기

- 계산량이 큰 파생 값은 `useMemo`로 **캐시** (메모이제이션)한다. 값의 **정확성은 의존성으로 보장**하고, 캐싱은 **성능 최적화**로만 사용한다.

```tsx
const sorted = useMemo(() => heavySort(items), [items]);
```

> **주의하세요!**  
> `useMemo`는 **결과를 바꾸지 않는다.** 의존성이 바뀌면 다시 계산한다. 조기 최적화는 지양하며, **측정 후** 적용한다.

### prop 변경 시 모든 state 초기화

- 특정 prop이 바뀔 때 자식의 **내부 state를 전부 초기화**하려면 자식에 **`key`** 를 부여한다. 키가 바뀌면 React는 컴포넌트를 **재마운트**한다.

```tsx
// parent
<Editor key={documentId} initialContent={content} />
```

### prop이 변경될 때 일부 state 조정하기

- 일부만 조정이 필요하면 **렌더 중 계산으로 안전하게 보정**한다. 사용자가 입력 중인 state를 덮어쓰지 않도록 **이벤트에서만 setState** 한다.

```tsx
function Pagination({ page, pageCount }: { page: number; pageCount: number }) {
  // 렌더 중 범위 보정(표시는 안전)
  const current = Math.min(Math.max(page, 1), pageCount);
  // 이동은 이벤트에서만 수행
  const [p, setP] = useState(current);
  const go = (n: number) => setP(Math.min(Math.max(n, 1), pageCount));
  return (
    <div>
      Page {p}/{pageCount} <button onClick={() => go(p + 1)}>Next</button>
    </div>
  );
}
```

### 이벤트 핸들러 간 로직 공유

- Effect로 상태를 옮기지 말고 **순수 함수**로 로직을 추출하여 **여러 핸들러에서 재사용**한다. 필요 시 **커스텀 훅**으로 구성하되, 외부 동기화가 없다면 Effect는 포함하지 않는다.

```tsx
function computeDiscount(price: number, coupon?: string) {
  /* ... */
}
function Buy({ price }: { price: number }) {
  const onApply = (code: string) => computeDiscount(price, code);
  const onCheckout = () => computeDiscount(price);
  // ...
}
```

### POST 요청 보내기

- 네트워크 **쓰기(POST, PATCH, DELETE)** 는 **이벤트 핸들러**에서 실행한다. Effect로 전송하면 재렌더/복원으로 **중복 전송**될 수 있다.

```tsx
const onSubmit = async (form: FormData) => {
  await fetch('/api/checkout', { method: 'POST', body: form });
};
```

### 연쇄 계산

- A에서 B를, B에서 C를 만드는 **연쇄 파생**은 Effect가 아니라 **순서 있는 렌더 계산**으로 해결한다. 성능이 문제면 각 단계를 `useMemo`로 감싼다.

```tsx
const filtered = useMemo(() => filter(items, query), [items, query]);
const sorted = useMemo(() => sort(filtered, order), [filtered, order]);
const page = useMemo(() => paginate(sorted, pageNo), [sorted, pageNo]);
```

### 애플리케이션 초기화

- 앱 전역 초기화(폴리필, 로거 설정 등)는 **모듈 스코프**나 부트스트랩 코드에서 1회 수행한다. 컴포넌트 Effect는 다중 마운트로 **중복 초기화** 위험이 있다.

```tsx
// setup.ts (모듈 스코프)
initSentry();
configureDayjs();
```

### state 변경을 부모 컴포넌트에게 알리기

- 자식이 부모의 state를 바꾸게 하려면 **콜백 prop**(예: `onChange`)을 호출한다. Effect로 상향 알림을 시도하면 **불필요한 추가 렌더**와 **순환 의존**을 초래한다.

```tsx
function Child({ value, onChange }: { value: string; onChange: (v: string) => void }) {
  return <input value={value} onChange={(e) => onChange(e.target.value)} />;
}
```

### 부모에게 데이터 전달하기

- 폼 제출/선택 변경 등 **사용자 행동**을 통해 부모로 데이터를 전달한다. 상태가 여러 컴포넌트에 필요하면 **state 끌어올리기** 또는 **Context**를 고려한다.

### 외부 저장소 구독하기

- 외부 스토어(브라우저/SDK/유틸)는 직접 Effect로 구독하지 말고 **`useSyncExternalStore`** 를 사용한다. Concurrent/SSR에서 **찢어짐 없이 일관성**을 보장한다.

```tsx
function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine,
    () => true
  );
}
```

### 데이터 가져오기

- 클라이언트 Effect의 페칭은 **수동 관리 비용**이 높다(중복 요청, 경합, 캐시 부재). 권장 대안은 **라우터/서버 컴포넌트의 데이터 로딩**, 또는 **React Query/SWR** 같은 라이브러리이다. 이들은 **캐시·중복 제거·재시도·동기화**를 제공한다.
- 꼭 Effect에서 페칭할 때는 **`AbortController`** 로 레이스를 방지하고, 의존성에 **정확한 키**를 사용한다.

```tsx
useEffect(() => {
  const ac = new AbortController();
  fetch(url, { signal: ac.signal }).then(/* ... */);
  return () => ac.abort();
}, [url]);
```

> **ref 콜백을 사용하여 ref 리스트 관리하기**  
> DOM 요소 목록을 후처리(스크롤/측정)해야 하는 경우, Effect에서 `querySelectorAll`로 찾기보다 **콜백 ref**를 사용해 항목 id → 노드 맵을 관리한다. 콜백은 마운트/언마운트 시 각각 호출되므로, 등록/해제를 **멱등적**으로 처리할 수 있다.

## 요약

- 렌더링 중에 무언가를 계산할 수 있다면 Effect가 필요하지 않다.
- 비용이 많이 드는 계산을 캐시하려면 `useEffect` 대신 `useMemo`를 사용한다.
- 전체 컴포넌트 트리의 state를 초기화하려면 다른 `key`를 전달한다.
- prop 변경에 대한 응답으로 특정 state bit를 초기화하려면 렌더링 중에 안전하게 계산한다.
- 컴포넌트가 표시되어 실행되는 코드는 Effect에 두고, 나머지는 이벤트 핸들러에 둔다.
- 여러 컴포넌트의 state를 업데이트해야 하는 경우 단일 이벤트 중에 수행하는 것이 바람직하다.
- 다른 컴포넌트의 state 변수를 동기화하려고 할 때마다 **state 끌어올리기**를 고려한다.
- Effect로 데이터를 가져올 수 있으나 경쟁 상태를 피하기 위해 정리를 구현해야 한다.

## 인사이트

- Effect는 **외부 시스템 동기화용 최소한의 도구**로 사용한다. **파생 계산/이벤트/키/메모**로 대체 가능한지 먼저 검토한다.
- “두 소스의 진실”을 만들지 말고 **단일 소스**를 유지한다. 필요 시 부모로 끌어올리고, 공유 범위가 넓다면 Context를 사용한다.
- 데이터는 **서버 주도 로딩 또는 전용 캐싱 라이브러리**를 우선한다. 클라이언트 Effect 페칭은 예외적으로만 사용한다.
- 외부 구독은 `useSyncExternalStore`, DOM 조작은 **ref + 이펙트**, 최신 콜백은 **`useEvent`/ref 동기화**로 해결한다.
