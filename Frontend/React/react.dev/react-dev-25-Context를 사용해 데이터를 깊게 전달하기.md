# React.dev-docs-25-Context를 사용해 데이터를 깊게 전달하기

[REACT 학습하기 > STATE 관리하기 > Context를 사용해 데이터를 깊게 전달하기](https://ko.react.dev/learn/passing-data-deeply-with-context)

## Goal

보통 부모 → 자식으로 props를 통해 데이터를 전달한다.  
그러나 아래와 같은 경우 props 전달은 불편해진다:

1. 중간에 많은 컴포넌트를 거쳐야 하는 경우
2. 애플리케이션의 여러 컴포넌트에서 동일한 정보가 필요한 경우

→ **Context**를 사용하면 부모 컴포넌트가 트리 아래 모든 자식 컴포넌트에 props 전달 없이 데이터를 제공할 수 있다.

- “Prop drilling”의 문제
- Context로 반복적인 prop 전달 대체하기
- Context의 일반적인 사용 사례
- Context 사용 전 고려할 대안

---

## Props 전달하기의 문제점

- props를 통해 데이터를 전달하는 방식은 명시적이고 단순하다.
- 하지만 여러 중간 단계의 컴포넌트를 단순히 데이터를 전달하기 위해 거쳐야 할 때 불필요하게 복잡해진다.
- 이런 현상을 **prop drilling**이라 부른다.

---

## Context: Props 전달하기의 대안

- Context는 트리 깊숙한 컴포넌트에도 데이터를 바로 제공할 수 있다.
- Context는 크게 세 단계로 사용한다:

### 1단계: Context 생성하기

```jsx
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

### 2단계: Context 사용하기

```jsx
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext); //useContext 훅 사용
  // ...
}
```

### 3단계: Context 제공하기

```jsx
import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className='section'>
      <LevelContext value={level}>{children}</LevelContext>
    </section>
  );
}
```

`context provider`로 감싸주기.

> 지금까지 작업 방식에서는 `LevelContext.provider` 형식으로 provider를 제공해 왔는데, React 19 버전부터는 생략해도 되도록 변경되었다.
>
> 간편한 듯하다.
>
> 기존의 provider 코드보다 provider 를 제공해주는 것이 명시적이진 않은 것 같기도 하다.

## 같은 컴포넌트에서 context를 사용하며 제공하기

- 동일한 컴포넌트에서 Context의 값을 읽으면서 동시에 다른 자식에게 제공할 수도 있다.
- 이 방식은 상위에서 받은 context를 가공해 하위에 재전달할 때 유용하다.

```jsx
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className='section'>
      <LevelContext value={level + 1}>{children}</LevelContext>
    </section>
  );
}
```

Context를 통해 위의 컴포넌트에서 정보를 읽을 수 있으므로 각 Section은 위의 Section에서 level을 읽고 자동으로 level + 1을 아래로 전달할 수 있다.

> **중요합니다!**

## Context로 중간 컴포넌트 지나치기

- Context를 사용하면 “주변에 적응”하고 렌더링 되는 위치(또는 어떤 context)에 따라 자신을 다르게 표시하는 컴포넌트를 작성할 수 있다.
  - context 의 value 값을 유연하게 사용하기 때문.
- 중간에 있는 컴포넌트가 데이터를 단순 전달만 한다면 Context로 대체 가능하다.
- 예: 사용자 로그인 정보, 테마, 언어 등 여러 곳에서 동일하게 필요한 데이터에 적합하다.
- 서로 다른 React context는 영향을 주지 않는다.

### Context를 사용하기 전에 고려할 것

어떤 props를 여러 레벨 깊이로 전달해야 한다고 해서 해당 정보를 context에 넣어야 하는 것은 아니다.

context를 사용하기 전 고려해볼 몇 가지 대안들.

- Props 전달하기로 시작하기.

  - 사소한 컴포넌트들이 아니라면 여러 개의 props가 여러 컴포넌트를 거쳐 가는 것은 그리 이상한 일이 아니다.
  - 힘든 일처럼 느껴질 수 있지만 어떤 컴포넌트가 어떤 데이터를 사용하는지 매우 명확히 해준다. - 데이터의 흐름이 props를 통해 분명해져 코드를 유지보수 하기에도 좋다.

- 컴포넌트를 추출하고 JSX를 children으로 전달하기.
  - 데이터를 사용하지 않는 많은 중간 컴포넌트 층을 통해 어떤 데이터를 전달하는 (더 아래로 보내기만 하는) 경우에는 컴포넌트를 추출하는 것을 잊은 경우가 많다.
  - 예를 들어 `posts`처럼 직접 사용하지 않는 props를 `<Layout posts={posts} />`와 같이 전달할 수 있다.
  - 대신 `Layout`은 `children`을 `prop`으로 받고 `<Layout><Posts posts={posts} /><Layout>`을 렌더링할 것.
  - 이렇게 하면 데이터를 지정하는 컴포넌트와 데이터가 필요한 컴포넌트 사이의 층수가 줄어든다.

> 만약 이 접근 방법들이 잘 맞지 않는다면 `context`를 고려해볼 것!

### Context 사용 예시

**테마 지정하기**:

- 사용자가 모양을 변경할 수 있는 애플리케이션의 경우에 (e.g. 다크 모드) context provider를 앱 최상단에 두고 시각적으로 조정이 필요한 컴포넌트에서 context를 사용할 수 있다.

**현재 계정**:

- 로그인한 사용자를 알아야 하는 컴포넌트가 많을 수 있다. Context에 놓으면 트리 어디에서나 편하게 알아낼 수 있다.
- 일부 애플리케이션에서는 동시에 여러 계정을 운영할 수도 있다. (e.g. 다른 사용자로 댓글을 남기는 경우.) 이런 경우에는 UI의 일부를 서로 다른 현재 계정 값을 가진 provider로 감싸 주는 것이 편리하다.

**라우팅**:

- 대부분의 라우팅 솔루션은 현재 경로를 유지하기 위해 내부적으로 context를 사용한다.
- 이것이 모든 링크의 활성화 여부를 “알 수 있는” 방법.

**상태 관리**:

- 애플리케이션이 커지면 결국 앱 상단에 수많은 state가 생기게 될 것. 하위의 멀리 떨어진 많은 컴포넌트가 그 값을 변경하고 싶어할 수 있음.
- 흔히 reducer를 context와 함께 사용하는 것은 복잡한 state를 관리하고 번거로운 작업 없이 멀리 있는 컴포넌트까지 값을 전달하는 방법이다.

## 요약

- Context는 컴포넌트가 트리 상 아래에 위치한 모든 곳에 데이터를 제공하도록 한다.
- Context를 전달 절차
  1. `export const MyContext = createContext(defaultValue)`로 context를 생성 및 내보내기.
  2. `useContext(MyContext)` Hook에 전달해 얼마나 깊이 있든 자식 컴포넌트가 읽을 수 있도록 하기.
  3. 자식을 `<MyContext value={...}>`로 감싸 부모로부터 context를 받도록 하기.
- Context는 중간의 어떤 컴포넌트도 지나갈 수 있다.
- Context를 활용해 “주변에 적응하는” 컴포넌트를 작성할 수 있다.
- Context를 사용하기 전에 props를 전달하거나 JSX를 `children`으로 전달하는 것을 먼저 시도해보기.

## 인사이트

- React 19 업데이트 이후, `MyContext.prvider value={...}`형태의 provider 씌우기가, `<MyContext value={...}>` 로 간소화 됨.
- Context는 prop drilling 문제를 해결하는 강력한 도구
  - 남용하면 오히려 컴포넌트 재사용성과 독립성을 해칠 수 있음. 설계의 중요성
  - “전역 상태 관리”가 필요한 순간에만 적용하는 것이 바람직.
- Context는 Redux, Zustand 같은 외부 상태 관리 라이브러리의 기초 개념과도 이어지며, 리액트의 상태 관리 스펙트럼에서 중요한 교차점을 차지함.
- Props로 충분한 상황과 Context가 필요한 상황을 구분하는 능력이 React 개발자의 설계 역량을 드러냄. -> 좋은 코드를 보고 학습
