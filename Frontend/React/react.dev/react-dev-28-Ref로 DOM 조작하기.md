# React.dev-docs-28-Ref로 DOM 조작하기

[REACT 학습하기 > 탈출구 > Ref로 DOM 조작하기](https://ko.react.dev/learn/manipulating-the-dom-with-refs)

## Goal

React는 렌더링 결과물에 맞춰 DOM 변경을 자동으로 처리하므로 컴포넌트에서 DOM을 직접 조작할 필요는 많지 않다.  
다만 **_특정 노드에 포커스를 옮기거나, 스크롤 위치를 이동하거나, 위치와 크기를 측정해야 하는 경우_** 에는 React가 관리하는 DOM 요소에 접근해야 한다.  
 React는 이러한 작업을 위한 내장 메서드를 제공하지 않으므로 **DOM 노드에 접근하기 위한 ref**를 사용한다.

- ref 어트리뷰트로 React가 관리하는 DOM 노드에 접근하는 법을 이해한다.
- ref JSX 어트리뷰트와 `useRef` Hook의 관련성을 파악한다.
- 다른 컴포넌트의 DOM 노드에 접근하는 안전한 방법을 습득한다.
- React가 관리하는 DOM을 수정해도 안전한 경우와 피해야 할 경우를 구분한다.

## ref로 노드 가져오기

- `useRef(initialValue)`는 `{ current: initialValue }` 형태의 컨테이너를 반환한다. React는 커밋 단계에서 `<div ref={myRef}>`와 같이 지정된 노드 참조를 `myRef.current`에 넣는다.
- `ref.current`는 렌더 사이에 유지되며 값을 바꾸어도 리렌더링을 트리거하지 않는다. 따라서 포커스 이동, 스크롤 조작, 치수 측정과 같은 **명령형 작업**에 적합하다.

```tsx
import { useEffect, useRef } from 'react';

export default function FocusOnMount() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    inputRef.current?.focus();
  }, []);

  return <input ref={inputRef} placeholder='마운트 시 포커스됨' />;
}
```

### 예시: 텍스트 입력에 포커스 이동하기

```tsx
function Search() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focus = () => inputRef.current?.focus();
  const selectAll = () => inputRef.current?.select();
  const clear = () => {
    if (!inputRef.current) return;
    inputRef.current.value = '';
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} placeholder='검색어 입력' />
      <button onClick={focus}>포커스</button>
      <button onClick={selectAll}>전체 선택</button>
      <button onClick={clear}>지우기</button>
    </div>
  );
}
```

### 예시: 한 요소로 스크롤을 이동하기

```tsx
function ScrollToSection() {
  const sectionRef = useRef<HTMLDivElement>(null);

  const scroll = () => {
    sectionRef.current?.scrollIntoView({ behavior: 'smooth', block: 'center' });
  };

  return (
    <>
      <button onClick={scroll}>섹션으로 이동</button>
      {/* ...긴 콘텐츠... */}
      <div ref={sectionRef} style={{ height: 400, background: '#f5f5f5' }}>
        타깃 섹션
      </div>
    </>
  );
}
```

> **ref 콜백을 사용하여 ref 리스트 관리하기**  
> 리스트 항목마다 `useRef`를 만드는 대신 **콜백 ref**를 사용해서 항목 id → DOM 노드 맵을 관리한다. 콜백은 노드가 마운트/언마운트될 때 각각 호출되므로, 등록/해제를 일관되게 처리할 수 있다.
>
> ```tsx
> function List({ items }: { items: { id: string; label: string }[] }) {
>   const nodes = useRef(new Map<string, HTMLLIElement>());
>   const setNode = (id: string) => (node: HTMLLIElement | null) => {
>     if (node) nodes.current.set(id, node);
>     else nodes.current.delete(id);
>   };
>   const scrollTo = (id: string) => nodes.current.get(id)?.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
>   return (
>     <>
>       <button onClick={() => scrollTo(items[items.length - 1].id)}>마지막으로</button>
>       <ul>
>         {items.map((it) => (
>           <li key={it.id} ref={setNode(it.id)}>
>             {it.label}
>           </li>
>         ))}
>       </ul>
>     </>
>   );
> }
> ```
>
> **중요합니다!**  
> 개발 모드에서 **Strict Mode**가 활성화되어 있으면 React는 잠재적 부작용 탐지를 위해 일부 동작을 **마운트→언마운트→재마운트**로 시뮬레이션한다. 이로 인해 **콜백 ref가 두 번 호출**될 수 있다(`node`로 한 번, `null`로 한 번). 콜백 ref 내부 로직은 **멱등(idempotent)** 하게 작성하고, 외부 구독/타이머 등은 **정리(cleanup)** 를 정확히 수행해야 한다.

## 다른 컴포넌트의 DOM 노드 접근하기

- 사용자 정의 컴포넌트에 `ref`를 곧바로 전달해도 DOM 노드에 접근되지 않는다. DOM 노드를 외부에 제공하려면 **`forwardRef`** 로 전달하거나, 더 바람직하게는 **명령형 API**를 노출한다.

```tsx
import { forwardRef, useImperativeHandle, useRef } from 'react';

type InputHandle = { focus: () => void; select: () => void };

const FancyInput = forwardRef<InputHandle, JSX.IntrinsicElements['input']>((props, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
    select: () => inputRef.current?.select(),
  }));
  return <input ref={inputRef} {...props} />;
});

// 부모 사용
function Parent() {
  const apiRef = useRef<InputHandle>(null);
  return (
    <>
      <FancyInput ref={apiRef} placeholder='forwardRef + imperative API' />
      <button onClick={() => apiRef.current?.focus()}>포커스</button>
    </>
  );
}
```

> **주의하세요!**  
> 전체 DOM 노드 참조를 그대로 노출하면 부모가 자식의 내부 구현을 과도하게 의존하게 된다. 가능한 경우 **필요한 명령만 노출하는 좁은 인터페이스**(예: `focus()`, `scrollToEnd()`)로 제한한다. 이는 캡슐화를 유지하여 리팩터링 내성이 커진다.

> **명령형 처리방식으로 하위 API 노출하기**  
> `useImperativeHandle`을 통해 특정 메서드만 공개하여 부모가 호출할 수 있게 한다. 이 방식은 접근을 통제하고, 내부 DOM 구조 변경 시 외부 계약을 안정적으로 유지한다.

## React가 ref를 부여할 때

- 커밋 단계에서 React는 **마운트 시 `ref.current = 노드`**, **언마운트 시 `ref.current = null`** 로 설정한다. 키 변경이나 조건부 렌더링으로 노드가 바뀌면 새 노드로 갱신된다.
- 객체 ref(`useRef`)와 콜백 ref 모두 지원되며, 콜백 ref는 노드의 수명 주기에 따라 여러 번 호출될 수 있다.

> **flushSync로 state 변경을 동적으로 플러시하기**  
> DOM을 조작하기 전에 해당 DOM이 최신 렌더 결과를 반영하도록 보장해야 한다. 예를 들어 항목을 추가한 직후 **추가된 항목으로 스크롤**하려면, 다음과 같이 동기 플러시를 통해 DOM 업데이트를 먼저 커밋한다.
>
> ```tsx
> import { flushSync } from 'react-dom';
>
> function Chat() {
>   const [messages, setMessages] = useState<string[]>([]);
>   const endRef = useRef<HTMLDivElement>(null);
>
>   const addMessage = (text: string) => {
>     flushSync(() => {
>       setMessages((prev) => [...prev, text]); // DOM을 먼저 최신화
>     });
>     endRef.current?.scrollIntoView({ behavior: 'smooth' });
>   };
>
>   return (
>     <div style={{ maxHeight: 240, overflow: 'auto' }}>
>       {messages.map((m, i) => (
>         <div key={i}>{m}</div>
>       ))}
>       <div ref={endRef} />
>     </div>
>   );
> }
> ```

## ref로 DOM을 조작하는 모범 사례

- **렌더 중 Ref 접근 금지**: 렌더는 순수해야 하며, Ref 읽기/쓰기는 **이벤트 핸들러** 또는 **이펙트(`useEffect`/`useLayoutEffect`)** 에서 수행한다.
- **측정은 `useLayoutEffect`** 에서 수행한다: 레이아웃을 읽고(예: `getBoundingClientRect`) 즉시 동기적으로 스타일을 적용해야 할 때 깜빡임을 줄인다.
- **React가 관리하는 속성은 React로 제어**한다: `className`, `style`, `value` 등은 직접 DOM에 설정하지 말고 상태→렌더로 갱신한다.
- **명령형 API는 좁게 설계**한다: 필요한 동작만 노출하고 내부 DOM 구조에 대한 결합을 줄인다.
- **정리(cleanup)** 를 철저히 한다: 수동으로 등록한 이벤트 리스너·타이머·관찰자(ResizeObserver/IntersectionObserver)는 이펙트 정리 함수에서 해제한다.
- **접근성 고려**: 포커스 이동 시 키보드 탐색 흐름을 방해하지 않으며, 필요 시 `tabIndex`, `aria-*` 속성을 함께 관리한다.

## 요약

- Ref는 일반적인 개념이지만, 대부분의 경우 DOM 요소를 저장하는 데 사용한다.
- React에 `<div ref={myRef}>`와 같이 작성하면, 해당 DOM 노드를 `myRef.current`에 넣도록 지시하는 것이다.
- 대부분의 경우, Ref는 DOM 요소에 포커스를 주거나, 스크롤하거나, 치수를 측정하는 등 DOM을 직접 변경하지 않고 상태를 유지하는 작업에 사용한다.
- 컴포넌트는 기본적으로 자신의 DOM 노드를 외부에 노출하지 않는다. `ref` Prop을 사용하여 DOM 노드를 선택적으로 노출할 수 있다.
- React가 관리하는 DOM 노드를 직접 변경하는 것은 피해야 한다.
- 불가피하게 React가 관리하는 DOM 노드를 수정해야 한다면, React가 업데이트할 이유가 없는 부분만 수정해야 한다.

## 인사이트

- Ref는 **명령형 인터랙션의 접점**으로 사용하며, UI 상태 표현은 여전히 선언적으로 유지하는 것이 바람직하다.
- 콜백 ref와 `forwardRef`/`useImperativeHandle`은 **캡슐화와 유연성**을 동시에 확보하는 수단이다.
- DOM 측정·동기 조작이 필요한 경우 `useLayoutEffect`와 `flushSync`를 적절히 활용하여 **깜빡임과 레이스 컨디션**을 최소화한다.
  - `useLayoutEffect` & `flushSync` 개념 및 적용 학습
- React가 관리하는 속성과 수동 DOM 조작 사이의 경계를 명확히 하여 **충돌과 유지보수 비용**을 줄인다.
