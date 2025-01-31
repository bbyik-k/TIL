# TanStack Query

- 공식 사이트: [TanStack Query](https://tanstack.com/query/latest)
- DOCS: [Overview | TanStack Query React Docs](https://tanstack.com/query/latest/docs/framework/react/overview)

- TanStack의 의미

  - Tan: 창립자 Tanner Linsley의 이름에서 따온 것.
  - Stack: 다양한 라이브러리들이 함께 제공된다는 의미.
  - TanStack은 단일 라이브러리가 아니라 "React Query", "TanStack Table", "TanStack Router" 등 여러 라이브러리를 포함한 프로젝트 스택.

- 사용 목적

  - 기존 Hook 의 문제
    - Hook 은 값의 재사용이 아닌, 로직의 재사용을 위한 것
    - 따라서\!
      - 값의 Cache가 되지 않음. \- 새롭게 data를 계속 받아옴.
      - Retry 기능의 부재: loading, error 에 대한 처리는 하지만, 네트워크 통신 실패 시 다시 재시도 하는 기능의 결여. \- 개발자(본인)가 구현해두지 않음.
  - React query (TanStack query) 는 이러한 문제들을 해결.

    - loading, error, success 처리
    - 같은 data caching 처리
    - global 상태 관리
    - fail 시 일정 시간 후 retry 기능 제공
    - 네트워크 통신, 비동기적 데이터 관리 시 유용하게 이용 가능

    - 상태관리

# TanStack Query 사용 시 팁과 주의사항

## ✅ 기본 사용법

### 1. 설치

```sh
yarn add @tanstack/react-query
# 또는
npm install @tanstack/react-query
```

### 2. QueryClient 설정

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <YourComponent />
    </QueryClientProvider>
  );
}
```

---

## 🚀 사용 팁

### 1. `useQuery`의 `staleTime`과 `cacheTime` 설정하기

- **`staleTime`**: 데이터를 "신선한 상태"로 유지하는 시간.
- **`cacheTime`**: 데이터를 메모리에서 캐시로 유지하는 시간.

```tsx
const { data } = useQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  staleTime: 1000 * 60 * 5, // 5분 동안 fresh 상태 유지
  cacheTime: 1000 * 60 * 10, // 10분 동안 캐시 유지
});
```

### 2. 쓸데없는 리렌더링 방지 (`select` 사용)

- `useQuery`의 `select` 옵션을 사용하면 불필요한 데이터 필터링 가능.

```tsx
const { data } = useQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  select: (data) => data.filter((product) => product.onSale),
});
```

### 3. `useMutation` 사용 시 `invalidateQueries` 활용

- 데이터를 변경한 후 기존 데이터를 다시 불러오려면 `queryClient.invalidateQueries` 사용.

```tsx
const queryClient = useQueryClient();
const mutation = useMutation(updateProduct, {
  onSuccess: () => {
    queryClient.invalidateQueries(['products']);
  },
});
```

---

## ⚠️ 주의해야 할 사항

### 1. `useQuery`는 반드시 컴포넌트 내부에서 호출해야 함

```tsx
function MyComponent() {
  const { data } = useQuery({ queryKey: ['products'], queryFn: fetchProducts });
}
```

❌ **잘못된 예시:**

```tsx
const { data } = useQuery({ queryKey: ['products'], queryFn: fetchProducts }); // 최상위에서 호출하면 오류 발생
```

### 2. `useQueryClient`도 반드시 컴포넌트 내부에서 사용해야 함

```tsx
function MyComponent() {
  const queryClient = useQueryClient();
}
```

### 3. `staleTime`을 너무 짧게 설정하면 API 요청이 과다 발생할 수 있음

```tsx
useQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  staleTime: 500, // ❌ 너무 짧음 → API 요청 과부하 발생 가능
});
```

✅ 적절한 `staleTime` 설정 권장 (예: 5분 이상)

### 4. `useMutation`에서 성공 후 동작을 `onSuccess`에서 처리할 것

```tsx
const mutation = useMutation(updateProduct, {
  onSuccess: () => {
    queryClient.invalidateQueries(['products']);
  },
});
```

### 5. 서버 응답 구조가 다를 경우 `select`로 변환하기

```tsx
const { data } = useQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  select: (data) => data.products, // 서버 응답이 { products: [...] } 형태일 때
});
```

---

## 🎯 결론

- `staleTime`과 `cacheTime`을 적절하게 설정하여 API 요청 최적화
- `useQueryClient`, `useQuery`는 반드시 컴포넌트 내부에서 호출
- `useMutation` 성공 후 `invalidateQueries`로 데이터 갱신
- `select` 옵션을 활용하여 불필요한 데이터 필터링
