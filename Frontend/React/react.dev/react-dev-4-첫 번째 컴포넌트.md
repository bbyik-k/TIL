# React.dev-docs-4-UI 표현하기-첫 번째 컴포넌트

[REACT 학습하기 > UI 표현하기 > 첫 번째 컴포넌트](https://ko.react.dev/learn/your-first-component)

## Goal

- 컴포넌트가 무엇일까
- React 애플리케이션에서 컴포넌트의 역할
- 첫 번째 React 컴포넌트를 작성하는 방법

---

## 컴포넌트: UI 구성 요소

- 컴포넌트는 UI를 구성하는 가장 작은 단위
- HTML 태그처럼 보이지만, 개발자가 정의한 새로운 태그 역할 수행
- 앱의 UI는 여러 컴포넌트의 조합으로 구성됨

## 컴포넌트 정의하기

### 1단계: 컴포넌트 내보내기

- `export default function MyComponent() { ... }` 형식으로 작성

- 다른 파일에서도 불러와 사용할 수 있도록 `export` 사용

- 가장 흔한 형태는 `export default`이며, 파일당 대표 컴포넌트를 지정한다.
  ```jsx
  export default function Profile() {
    return <img src='https://img.com' alt='exampleImg' />;
  }
  ```

### 2단계: 함수 정의하기

- 컴포넌트는 일반 JavaScript 함수처럼 작성

- 반환값으로 JSX를 작성해야 함

- React 컴포넌트는 일반 JavaScript 함수이지만, 이름은 `대문자`로 시작해야 하며 그렇지 않으면 작동하지 않음.

```jsx
function Profile() {
  /* ... */
} // ✅ 대문자 시작
```

### 3단계: 마크업 추가하기

- JSX를 반환하여 브라우저에 렌더링

- HTML과 유사하지만, JavaScript 표현식을 중괄호 `{}`로 삽입 가능

- 여러 줄 반환 시 괄호로 감싸는 관례를 따름. 모든 태그는 닫아야 함.

## 컴포넌트 사용하기

### 브라우저에 표시되는 내용

- 정의한 컴포넌트는 `<MyComponent />`처럼 JSX에서 태그 형태로 사용

- React가 함수 호출을 대신 처리하고 JSX를 반환

### 컴포넌트 중첩 및 구성

- 컴포넌트 안에서 다른 컴포넌트를 포함 가능

- 작은 컴포넌트를 조립하여 큰 UI를 형성하는 구조

## 요약

- React를 사용하면 앱의 재사용 가능한 UI 요소인 컴포넌트를 만들 수 있음.

- React 앱에서 모든 UI는 컴포넌트임.

- React 컴포넌트는 다음 몇 가지를 제외하고는 일반적인 JavaScript 함수임.
  - 컴포넌트의 이름은 항상 대문자로 시작함.
  - JSX 마크업을 반환함.
