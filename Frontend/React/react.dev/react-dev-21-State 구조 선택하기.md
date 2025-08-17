# React.dev-docs-21-State 구조 선택하기

[REACT 학습하기 > STATE 관리하기 > State 구조 선택하기](https://ko.react.dev/learn/choosing-the-state-structure)

## Goal

수정과 디버깅이 즐거운 컴포넌트 vs 지속적인 버그의 원인이 되는 컴포넌트  
-> State를 잘 구조화 했을 때 차이를 만들어낼 수 있음.

- 단일 vs 다중 state 변수를 사용하는 경우
- State를 구성할 때 피해야 할 사항
- 상태 구조의 일반적인 문제를 해결하는 방법

## State 구조화 원칙

- 목표: **오류 없이 상태를 쉽게 업데이트하는 것**
  - state에서 불필요하고 중복된 데이터를 제거하면, 모든 데이터 조각이 동기화 상태를 유지하는 데 도움이 됨.
  - 데이터베이스 엔지니어의 데이터베이스 구조 “**_정규화(normalization)_**”와 유사한 개념.

#### 구조화 원칙

1. **연관된 state 그룹화 하기.**
   - 두 개 이상의 state 변수를 **항상 동시에 업데이트** 한다면, 단일 state 변수로 병합하는 것을 고려할 것
2. **State의 모순 피하기**
   - 여러 state 조각이 **서로 모순되고 “불일치”** 할 수 있는 방식으로 state를 구성하는 것은 실수가 발생할 여지를 만듦. 이를 피할 것.
3. **불필요한 state 피하기**
   - 렌더링 중에 컴포넌트의 props나 기존 state 변수에서 **일부 정보를 계산할 수 있다면**, 컴포넌트의 state에 해당 정보를 넣지 않아야 함.
4. **State의 중복 피하기**
   - 여러 상태 변수 간 또는 중첩된 객체 내에서 **동일한 데이터가 중복될 경우** 동기화를 유지하기 어려움. -> 가능한 중복을 줄일 것
5. **깊게 중첩된 state 피하기**
   - 깊게 **계층화된 state**는 업데이트 하기 쉽지 않음. -> 가능하면 state를 평탄한 방식으로 구성할 것

## 1. 연관된 state 그룹화하기

두 개 이상의 state 변수가 항상 동시에 업데이트된다면 **하나의 객체로 통합**하는 것이 좋음.

- 예시:
  ```js
  const [position, setPosition] = useState({ x: 0, y: 0 });
  ```
- 특히 필요한 state 조각 수를 미리 알 수 없는 경우(예: 사용자가 커스텀 필드를 추가하는 양식) 객체 단위 관리가 유리하다.

## 2. State의 모순 피하기

- 불가능한 상태를 허용하지 않는 구조를 만들 것.
- 예: `isSending`과 `isSent`가 동시에 `true`가 되는 모순.
- 개선:
  ```Js
  const [status, setStatus] = useState('typing'); // typing | sending | sent
  ```
- 세 가지 유효한 상태 중 **하나를 가질 수 있는** 상태 변수로 정의하면 상태 전이가 더 명확해지고, 타입 정의로 안전성을 높일 수 있다.

## 3. 불필요한 state 피하기

- 렌더링 중에 props나 다른 state에서 **계산 가능한 값**은 state로 두지 않는다.
- 예:

  ```js
  const fullName = firstName + ' ' + lastName;
  ```

- `firstName` 또는 `lastName`이 변경될 때마다 리렌더링이 발생 → `fullName`은 자동으로 갱신됨.

> **Props를 state에 미러링하지 마세요.**
>
> state는 최초 렌더링에서만 초기화되므로 부모가 새로운 props를 전달해도 반영되지 않는다.

## 4. State의 중복 피하기

- **동일한 데이터를 중복** 관리하면 동기화 문제가 발생한다.
- 잘못된 예시:

  ```js
  const [items, setItems] = useState(initialItems);
  const [selectedItem, setSelectedItem] = useState(items[0]); // 동일 객체 중복
  ```

- 개선된 예시:

  ```js
  const [items, setItems] = useState(initialItems);
  const [selectedId, setSelectedId] = useState(0); // 객체 대신 ID로 관리
  ```

## 5. 깊게 중첩된 state 피하기

- **깊게 중첩된(계층화된)** state는 업데이트가 어렵고, 실수 가능성이 높다.
- 예: 트리 구조 데이터를 업데이트할 때, 변경된 노드까지의 **모든 부모**를 복사해야 함.
- 개선: `정규화(flattening-평탄)` 구조 적용.
  - 각 노드가 자식 객체 대신 **자식 ID 배열**만 보유.
  - 데이터베이스 테이블처럼 관리 가능.
- 메모리 사용량 개선 → 삭제 시 관련된 항목과 자식들을 한 번에 정리.
- **Immer** 같은 라이브러리를 사용하면 반복되는 복사 로직을 간결하게 작성할 수 있다.

> **메모리 사용량 개선하기**
>
> 트리 평탄화 시 삭제된 항목과 그 자식들을 테이블 객체에서 제거해야 한다.
>
> 간결하게 사용하기 위해 Immer 사용.

## 요약

- 두 state 변수가 **항상 함께 업데이트** 된다면 하나로 **합치기**.
- **불가능**한 상태를 **만들지 않도록** state 설계.
- 불필요하고 **중복**된 state를 **피할 것**.
- 특별한 경우가 아니라면 **props를 state에 넣지 말 것**.
- 선택과 같은 UI 패턴: 객체 자체 대신 **ID 또는 인덱스**를 state로 관리.
- UI 전용 상태(선택, 포커스 등)는 도메인 데이터와 분리.
- **깊게 중첩된 state는 평탄화(normalization)** 를 고려.

## 인사이트

- React의 state를 데이터베이스 모델링과 유사하게 접근하는 방식. 평탄화 방식.
- 오류 가능성을 줄이는 state 구조 설계를 통한 좋은 컴포넌트 작성을 고민하자.
- 새삼 느껴지는 Immer와 Zustand/Redux Toolkit과 같은 도구의 중요성.
