# React.dev-docs-7-중괄호가 있는 JSX 안에서 자바스크립트 사용하기

[REACT 학습하기 > UI 표현하기 > 중괄호가 있는 JSX 안에서 자바스크립트 사용하기](https://ko.react.dev/learn/javascript-in-jsx-with-curly-braces)

## Goal

- 따옴표로 문자열을 전달하는 방법
- 중괄호가 있는 JSX 안에서 JavaScript `변수`를 참조하는 방법
- 중괄호가 있는 JSX 안에서 JavaScript `함수`를 호출하는 방법
- 중괄호가 있는 JSX 안에서 JavaScript `객체`를 사용하는 방법

## 따옴표로 문자열 전달하기

- JSX 어트리뷰트에 **따옴표(`""`)**를 사용하면 문자열 리터럴로 전달된다.
- 예: `<img className="avatar" />` → className에 문자열 `"avatar"` 전달.
- HTML과 동일하게 문자열 자체를 전달하는 가장 단순한 방식이다.

## 중괄호 사용하기: JavaScript 세계로 연결하는 창

- JSX 안의 **중괄호 `{}`** 는 JavaScript 표현식을 평가하는 특별한 문법이다.
- 문자열이 아닌 동적인 값, 변수, 함수 결과를 JSX 마크업 안에 삽입할 수 있다.
- 예: `<img className={user.imageUrl} />`

## 중괄호를 사용하는 곳

- JSX **태그 콘텐츠**: `<h1>{user.name}</h1>`
- JSX **어트리뷰트 값**: `<img src={user.imageUrl} />`
- 어트리뷰트의 `=` 뒤나 태그 내부에만 올 수 있다.

## ”이중 중괄호” 사용하기: JSX의 CSS와 다른 객체

- JSX 안에서 객체를 전달할 때는 **중괄호 안에 객체 리터럴**을 그대로 넣는다.
- 이때 객체 리터럴 자체가 중괄호를 쓰므로 “중괄호 안에 중괄호” 형태가 된다.
- 예: `<div style={{ backgroundColor: 'black' }} />`
- 첫 번째 `{}`: JSX → JS 표현식 삽입
- 두 번째 `{}`: JS → 객체 리터럴 표현

> **주의하세요!**
> 인라인 style 프로퍼티는 캐멀 케이스로 작성됩니다.  
> 예를 들어 HTML에서의 `<ul style="background-color: black">`은  
> 컴포넌트에서 `<ul style={{ backgroundColor: 'black' }}>`로 작성됩니다.

## JavaScript 객체와 중괄호에 대해서 더 알아보기

- JSX는 JavaScript 확장 문법이므로 객체, 배열, 함수 호출 등 모든 표현식을 넣을 수 있다.
- `user.name`, `user.getImageUrl()`, `formatDate(date)` 등 자유롭게 활용 가능하다.
- 단, `if` 문이나 `for` 문 같은 **문(Statements)** 은 JSX 안에서 사용할 수 없고, **표현식(Expression)** 만 가능하다.

## 요약

- 따옴표 안의 JSX 어트리뷰트는 문자열로 전달된다.
- 중괄호를 사용하면 JavaScript 논리와 변수를 마크업으로 가져올 수 있다.
- JSX 태그 내부 또는 어트리뷰트의 `=` 뒤에서 작동한다.
- `{{ 및 }}` 는 특별한 문법이 아니라 **중괄호 안에 들어 있는 JavaScript 객체**를 의미한다.
