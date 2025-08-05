# React.dev-docs-3-React로 사고하기

[REACT 학습하기 > 빠르게 시작하기 > React로 사고하기](https://ko.react.dev/learn/thinking-in-react)

## Goal

- 복잡한 UI를 React 컴포넌트로 분할하는 사고 방식 이해
- UI 상태를 분석하고 시각화하는 방법 습득
- 데이터 흐름과 상태의 위치 결정 원칙 이해
- 상태 보존, 초기화, 최적화 구조 설계 기반 마련

---

## Step 1: UI를 컴포넌트 계층으로 쪼개기

- 디자이너가 제공한 목업(mockup)과 JSON 데이터를 바탕으로 UI 시작
- 목업 상에서 구성 요소별로 박스를 그리고 이름 부여
- 단일 책임 원칙(`SRP`, Single Responsibility Principle)에 따라 기능 단위로 컴포넌트 구분 (예: `SearchBar`, `ProductRow`)
- 데이터 구조와 UI 구성 간 자연스러운 대응 가능성 인식
- 최종 계층 구조 예시:
  - `FilterableProductTable`
    - `SearchBar`
    - `ProductTable`
      - `ProductCategoryRow`
      - `ProductRow`

---

## Step 2: React로 정적인 버전 구현하기

- 우선 상호작용 없이 정적인 UI부터 구성
- 부모 컴포넌트에서 props 전달 방식으로 UI 데이터 흐름 구현
- props 기반 UI만으로 기능 구성 시작 (state는 이 단계에서 사용하지 않음)
- 컴포넌트 재사용성과 **단방향** 데이터 흐름 체감 가능

---

## Step 3: 최소한의 데이터만 이용해서 완벽하게 UI State 표현하기

- 상태(state)는 **변할 수 있고, 부모나 다른 상태로부터 계산 불가한 데이터**만 포함해야 한다
- 여섯 가지 후보 데이터 중 실제로 state가 되어야 할 것은:
  - 사용자 입력 필터 텍스트
  - 재고 여부 체크박스 값
- 나머지는 props 또는 계산된 값으로 처리하여 `DRY`(Don't Repeat Yourself, [중복 배제 원칙](https://ko.wikipedia.org/wiki/%EC%A4%91%EB%B3%B5%EB%B0%B0%EC%A0%9C)) 원칙 준수
  - 최소한의 state 파악 및 이용할 것

* state가 **아닌 것들**

  1. 시간이 지나도 변하지 않나요? -> 확실히 state가 아님.
  2. 부모로부터 Props를 통해 전달 되나요? -> 확실히 state가 아님.
  3. 컴포넌트 내의 다른 state나, Props를 가지고 계산 가능한가요? -> 절대로 state가 아님!

---

## Step 4: State가 어디에 있어야 할 지 정하기

- 각 상태를 사용하는 컴포넌트들을 확인하고, 공통 부모 컴포넌트 결정
- 공통 부모인 `FilterableProductTable`에 state를 둬야 하는 이유:
  - `SearchBar`와 `ProductTable` 모두 해당 state에 의존함
- 최종 구현 방식:
  - `filterText`, `inStockOnly`를 `FilterableProductTable`이 state로 갖고,
  - 이를 `SearchBar`와 `ProductTable`에 props로 전달

---

## Step 5: 역 데이터 흐름 추가하기

- props만으로는 사용자 입력을 처리할 수 없기 때문에 이벤트 콜백 전달 필요
- `onFilterTextChange`, `onInStockOnlyChange` 형태로 콜백을 만들어 하위 컴포넌트에 전달
- `SearchBar`에서 사용자 입력 시 해당 콜백을 호출, 상위에서 state 업데이트 가능
- 단방향 데이터 흐름 유지하면서도 하위에서 state 변경 가능 구조 완성

---

## 인사이트

- UI를 기능 단위로 분해하는 사고 방식이 React 설계의 핵심 출발점이다.
- 최소한의 state로도 UI의 전체 상태를 충분히 표현할 수 있음을 체감했다.
- state의 위치는 **공통 부모**에 두는 것이 원칙이며, 데이터 공유를 구조적으로 해결하는 방법이다.
- 역 데이터 흐름(하위 → 상위 state 변경)은 콜백 전달 방식으로 명확하고 간단하게 구현 가능하다.
- 이 챕터를 통해 습득한 구조적 사고방식이 이후 Hook, Context, 상태 관리 도구 학습의 기반이 될 수 있다.
