# 고급 내용들 (Deep Dive)

### 좋은 추상화란?

- IoC 패턴
- Bottom-UP 사고방식

- ComponentProps vs HTMLAttributes
- [추상이란 무엇일까](https://evan-moon.github.io/2023/01/15/what-is-abstract/)

JS 책

- [코어 자바스크립트 | 정재남 \- 교보문고](https://product.kyobobook.co.kr/detail/S000001766397) / 19,800원

  - 후기: [\[책리뷰\] 코어 자바스크립트](https://brunch.co.kr/@razelo/52)

- [모던 자바스크립트 Deep Dive \- 예스24](https://m.yes24.com/Goods/Detail/92742567) / 40,500원
  - 효율적 리딩: [모던 자바스크립트 Deep Dive 효율적으로 읽는 법(필독 목차, 안 봐도 되는 목차 정리)](https://youtu.be/RdbVLzAfB44?si=dIEw6ToozA_9-IWY)
  - 후기
    - [드디어 완독\! 모던 자바스크립트 Deep Dive 스터디 후기](https://velog.io/@broccolism/%EB%93%9C%EB%94%94%EC%96%B4-%EC%99%84%EB%8F%85-%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-Deep-Dive-%EC%8A%A4%ED%84%B0%EB%94%94-%ED%9B%84%EA%B8%B0)
    - [\[모던 자바스크립트 Deep Dive\] 공부 후기 & 주절주절](https://velog.io/@mskwon/%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-Deep-Dive-%EA%B3%B5%EB%B6%80-%ED%9B%84%EA%B8%B0-%EC%A3%BC%EC%A0%88%EC%A3%BC%EC%A0%88)

##

## 25\. 01\. 16 (목)

### React의 불변성이란?

- react의 상태는 **불변성(immutability)**
  - 변경해야 한다면 새로운 값, 새로운 객체, 새로운 배열로 만들어야 함.
  - 객체의 가변성 / 불변성
    - 객체의 생성 시 객체의 참조값 (reference 값) 만들어짐.
    - 새로운 참조값이 만들어져야 변경을 인지.
    - 같은 참조값 내에서 내부 사항을 변경해도 변경되지 않음.
  - 따라서 배열, 객체 등의 전체적인 껍데기를 모두 업데이트 해야한다.
- **불변성이란?**
  - 불변성은 객체나 데이터를 직접 변경하지 않고, 변경이 필요한 경우 **기존 데이터를 복사한 새로운 객체**를 생성하여 수정하는 것을 의미한다.
  - React는 **상태(state)의 참조(reference)**를 비교하여 상태가 변경되었는지 감지합니다.
    - 새로운 참조가 생성되면 React는 상태가 변경된 것으로 인식하고 UI를 다시 렌더링합니다.
- 객체를 직접 수정하면 발생할 수 있는 문제
  - React가 상태 변경을 감지하지 못함
    - 객체를 직접 수정하면 참조가 바뀌지 않으므로 React는 상태가 변경된 것으로 인식하지 못할 수 있습니다.
  - 상태 변경 추적이 어려움
    - 객체를 직접 수정하면, 코드에서 어떤 상태가 변경되었는지 추적하기 어렵습니다.
    - React는 상태를 변경할 때 새로운 객체를 생성하는 방식으로 상태 변경 과정을 예측 가능하고 디버깅하기 쉽게 만듭니다.
  - React 성능 최적화 기능과 충돌
    - React는 성능 최적화를 위해 **얕은 비교(shallow comparison)**를 수행합니다.
    - 얕은 비교는 참조가 바뀌었는지만 확인하기 때문에 객체를 직접 수정하면 최적화가 제대로 작동하지 않습니다.
- 올바른 상태 관리 방법: **불변성 유지**
  - 불변성을 유지하려면 어떻게 해야 할까?
    - 객체를 복사하여 수정합니다.
      - JavaScript의 ... 스프레드 연산자나 Object.assign을 사용해 새로운 객체를 생성한 후 수정합니다.
    - 배열도 복사 후 수정
      - map, filter 등을 사용하여 새로운 배열을 반환합니다.
- React 상태 관리 원칙
  - 상태를 직접 변경하지 말 것:
    - 상태는 항상 새로운 객체를 생성하여 업데이트해야 합니다.
  - 불변성을 유지:
    - 스프레드 연산자나 map, filter와 같은 메서드를 활용하여 불변성을 유지합니다.
  - 상태 변경이 예측 가능하도록 작성:
    - 상태 변경 로직은 항상 명확하게 작성하여 디버깅을 용이하게 합니다.
- 정리
  - React 상태 관리에서 불변성을 유지하지 않으면 React가 상태 변경을 감지하지 못하거나, 성능 최적화가 제대로 동작하지 않을 수 있습니다.
  - 객체를 직접 수정하면 상태 변경 추적이 어려워지고, 예상치 못한 동작을 초래할 가능성이 큽니다.
  - 항상 새로운 객체를 생성하여 상태를 변경하는 방식으로 React의 상태 관리 철학을 따르는 것이 중요합니다.

### 참조의 개념이 정확히 무엇인가?

- 참조(Reference)의 개념
  - JavaScript에서 **참조(reference)**란, 변수나 값의 실제 데이터를 가리키는 **메모리 주소**를 의미합니다.
  - 참조는 JavaScript에서 **값의 저장 방식**을 이해하는 데 중요한 개념입니다. **원시 타입**과 **참조 타입**의 차이를 통해 참조의 동작 방식을 더 쉽게 이해할 수 있습니다.

1. 원시 타입과 참조 타입

- **1)원시 타입 (Primitive Type)**
  - 데이터 자체가 변수에 저장됩니다.
  - 값이 변경되면 새로운 메모리 공간이 할당됩니다.
  - 원시 타입 예시
    - number, string, boolean, undefined, null, symbol, bigint
  - let a \= 10; _// 변수 'a'에 값 10이 저장됨_
  - let b \= a; _// 변수 'b'에도 값 10이 복사됨_
  -
  - a \= 20; _// 변수 'a'의 값 변경_
  - console.log(b); _// 출력: 10 (b는 영향을 받지 않음)_
  - 동작 설명:
    - b \= a는 a의 값을 복사하여 b에 저장합니다. 이후 a를 변경해도 b는 영향을 받지 않습니다.
    - 두 변수는 서로 독립적인 값을 가집니다.
- **2\) 참조 타입 (Reference Type)**
  - 변수는 데이터 자체를 저장하지 않고, 데이터가 저장된 메모리 주소를 참조합니다.
  - 값이 변경되면 동일한 메모리 주소를 참조하는 다른 변수에도 영향을 줍니다.
  - 참조 타입 예시:
    - object, array, function

let obj1 \= { name: "John" }; _// obj1은 객체의 메모리 주소를 참조_  
let obj2 \= obj1; _// obj2도 동일한 메모리 주소를 참조_

obj1.name \= "Doe"; _// obj1을 통해 객체의 name 속성을 수정_  
console.log(obj2.name); _// 출력: "Doe" (obj2도 영향을 받음)_

-
- 동작 설명:
  - obj1과 obj2는 같은 객체를 참조합니다. 따라서 obj1을 통해 수정한 내용이 obj2에서도 반영됩니다.
  - 이처럼 객체는 참조를 통해 공유됩니다.

###

###

### 얕은 복사 vs 깊은 복사

### 상태관리 라이브러리

- 상태관리는 정말 중요한 토픽\!
  - 객체, 불변성, 가변성 이해 잘 하기
  - react 에서 사용하는 상태는 읽기 전용, 불변성 유지 필요
    - 따라서 새롭게 생성 해가야 함, 복사 후 업데이트
    - 중첩된 객체나 배열이 존재 시 코드가 복잡해짐
      - 해결을 위해 상태관리 라이브러리 사용
- 상태관리 라이브러리
  - 이전 트렌드?
    - Redux
    - rxJava \- observer, subscribe 베이스의 라이브러리
    - MobX
    - Immer
  - 최신 트렌드? \- Hook 이용
    - useState()
    - useReducer()
    - contextAPI \- 글로벌 공통 상태 관리

```javascript
setPerson((*person*) \=\> ({
             ...*person*,
             mentors: *person*.mentors.map((*mentor*) \=\> {
               *if* (prev \=== *mentor*.name) {
                 *return* { ...*mentor*, name };
               }
               *return* *mentor*;
             }),
           }));

{           const index \= person.mentors.findIndex((*mentor*) \=\> *mentor*.name \=== prev);
           const newArr \= \[...person.mentors\];
           newArr\[index\].name \= name;
           setPerson((*prev*) \=\> ({ ...*prev*, mentors: newArr }));
         }
```

#### Reducer

- useReducer()
- Hooks API Reference
  - [Built-in React Hooks](https://react.dev/reference/react/hooks)
  - [Hook의 개요](https://ko.legacy.reactjs.org/docs/hooks-intro.html)
  - ko: [state 로직을 reducer로 작성하기](https://ko.react.dev/learn/extracting-state-logic-into-a-reducer)
  - ko: [useReducer – React](https://ko.react.dev/reference/react/useReducer)
- Reducer 를 사용하는 이유
  - 한 컴포넌트에서 state 업데이트가 여러 이벤트 핸들러로 분산되는 경우가 있습니다. 이 경우 컴포넌트를 관리하기 어려워집니다.
  - 따라서, 문제 해결을 위해 state를 업데이트하는 모든 로직을 reducer를 사용해 컴포넌트 외부의 단일 함수로 통합해 관리할 수 있습니다.
- reducer 함수란 무엇인가
- useState에서 useReducer로 리팩토링 하는 방법
- reducer를 언제 사용할 수 있는지
- reducer를 잘 작성하는 방법

## 25\. 01\. 17 (금)

#### Immer

- useImmer library 설치 후 사용
- 일반 객체 수정과 같이 할당 연산자 \= 사용하여 객체 수정 가능
  - 내부적으로는 상태를 새로운 객체로 만들어서 반환
- Immer Reference
  - Immer Doc: [Introduction to Immer](https://immerjs.github.io/immer/)
  - [객체 State 업데이트하기](https://ko.react.dev/learn/updating-objects-in-state#write-concise-update-logic-with-immer)

#### Form

- React 의 철학은 React 내의 상태의 변화에 따른 UI의 변경이 기본\\
- 입력 from의 경우 **uncontrolled component**
- From, input & React Reference
  - [\<input\> – React](https://ko.react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)
- From input 같은 경우 키보드 입력 값 하나하나에 대해 리렌더링이 일어날 경우 성능 저하
  - UI 일부에 대해 리렌더링 지연하기
  - useDeferredValue Hook
  - Reference: [useDeferredValue – React](https://ko.react.dev/reference/react/useDeferredValue#deferring-re-rendering-for-a-part-of-the-ui)

#### children

- JSX 태그 내 콘텐츠 중첩
- Reference
  - 자식을 JSX로 전달하기: [컴포넌트에 props 전달하기 – React](https://ko.react.dev/learn/passing-props-to-a-component#passing-jsx-as-children)
  - (레거시) children API: [Children – React](https://ko.react.dev/reference/react/Children)

#### Context

- Reference
  - useContext: [useContext – React](https://ko.react.dev/reference/react/useContext)

#### Props Driling

- 컴포넌트 트리에서 데이터를 하위 컴포넌트로 전달하기 위해 중간 컴포넌트를 통해 프로퍼티를 내려주는 것을 의미.
- 이러한 중간 컴포넌트는 원하는 자식 컴포넌트에게 프로퍼티를 전달하기 위해 필요하지만 해당 값을 직접 사용하지 않는 경우에도 프로퍼티를 받고 전달
