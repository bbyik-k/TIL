# React.dev-docs-8-컴포넌트에 props 전달하기

[REACT 학습하기 > UI 표현하기 > 컴포넌트에 props 전달하기](https://ko.react.dev/learn/passing-props-to-a-component)

## Goal

- 컴포넌트에 props를 전달하는 방법
- 컴포넌트에서 props를 읽는 방법
- props의 기본값을 지정하는 방법
- 컴포넌트에 JSX를 전달하는 방법
- 시간에 따라 props가 변하는 방식

## 친숙한 props

- HTML 속성과 유사하게 React 컴포넌트에 **props**를 전달할 수 있다.
- 차이점은 HTML 속성은 문자열만 전달할 수 있지만, React props는 **문자열, 숫자, 객체, 함수, JSX 등 모든 값**을 전달할 수 있다.
- React 컴포넌트는 함수를 호출하는 것과 같으며, **JSX 속성 = 함수 인자**로 대응된다.

## 컴포넌트에 props 전달하기

### 1단계: 자식 컴포넌트에 props 전달하기

- 부모 컴포넌트에서 자식 컴포넌트로 데이터를 넘길 때 props를 사용한다.
- 예:
  ```jsx
  <Avatar person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }} size={100} />
  ```

### 2단계: 자식 컴포넌트 내부에서 props 읽기

- 함수의 매개변수로 props를 받아 사용한다.
- 구조 분해 할당 패턴을 주로 사용:

  ```jsx
  function Avatar({ person, size }) {
    return <img src={person.imageUrl} width={size} height={size} />;
  }
  ```

## prop의 기본값 지정하기

- 매개변수 기본값 문법을 활용해 누락된 props에 기본값을 설정할 수 있다.

  ```jsx
  function Avatar({ person, size = 100 }) {
    return <img src={person.imageUrl} width={size} height={size} />;
  }
  ```

  > **기본값의 사용**  
  > 이 기본값은 size prop이 없거나 size={undefined}로 전달될 때 사용됨.  
  > 그러나 size={null} 또는 size={0}으로 전달된다면, 기본값은 사용되지 않음.

## JSX spread 문법으로 props 전달하기

- 이미 정의된 props 객체를 spread 문법으로 전달할 수 있다.

  ```jsx
  const props = { person: user, size: 100 };
  <Avatar {...props} />;
  ```

- 코드가 간결해지지만, 지나치게 사용하면 컴포넌트 인터페이스를 모호하게 만들 수 있다.

## 자식을 JSX로 전달하기

- JSX 태그 사이에 작성된 내용은 children prop으로 전달된다.

  ```jsx
  <Card>
    <Avatar person={user} />
  </Card>
  ```

- Card 컴포넌트에서는 props.children으로 접근 가능하다.
- children prop을 가지고 있는 컴포넌트는 부모 컴포넌트가 임의의 JSX로 **“채울” 수 있는** `“구멍”`이 있는 것으로 생각할 수 있다.

<!-- <img width="580" height="289" alt="Image" src="https://github.com/user-attachments/assets/2ec96353-6098-460d-bd3b-169035cbc77a" /> -->

<!-- ![props 구멍](https://i.imgur.com/K9HVAGHl.jpg) -->

<!-- ![props 구멍](https://github.com/user-attachments/assets/2ec96353-6098-460d-bd3b-169035cbc77a) -->
</br>

![props 구멍](https://github-production-user-asset-6210df.s3.amazonaws.com/47810773/479884358-2ec96353-6098-460d-bd3b-169035cbc77a.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20250820%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250820T075924Z&X-Amz-Expires=300&X-Amz-Signature=b3cc883b45e325c9d45f6cf1435de5a4c9e2aa0cbc1fd094fd16263539ccaabd&X-Amz-SignedHeaders=host)

Illustrated by [Rachel Lee Nabors](https://nearestnabors.com/)

## 시간에 따라 props가 변하는 방식

- props는 읽기 전용 스냅샷이다.
  - "변경할 수 없다" 라는 의미의 [**불변성**](https://en.wikipedia.org/wiki/Immutable_object)을 가지고 있음.
- 부모가 다시 렌더링되면 자식은 새로운 props를 받는다.
- props는 직접 수정할 수 없으며, 수정이 필요하다면 state로 승격해야 한다.

## 요약

- Props를 전달하려면 HTML 어트리뷰트와 같은 방식으로 JSX에 추가한다.

- Props를 읽으려면 `function Avatar({ person, size })`처럼 구조 분해 할당을 사용한다.

- 기본값은 `size = 100`처럼 지정할 수 있다.

  - 이는 누락되거나, `undefined`인 props에 사용됨.

- 모든 props를 `<Avatar {...props} />`로 전달할 수 있지만(JSX spread 문법), 남용하지 않는다.

- `<Card><Avatar /></Card>`와 같이 중첩된 JSX는 children prop으로 나타난다.

- Props는 읽기 전용이며, 렌더링마다 새로운 스냅샷을 받는다.

- Props는 변경할 수 없다.
  - 상호작용이 필요한 경우 state 로 설정해야 한다.

## 인사이트

- children prop은 React 컴포넌트를 **범용 컨테이너 UI**로 확장할 수 있게 하는 핵심 개념이다.
- Props는 항상 **“읽기 전용”** 이라는 원칙을 통해 React의 **단방향 데이터 흐름(One-way Data Flow)** 철학이 구현된다.
