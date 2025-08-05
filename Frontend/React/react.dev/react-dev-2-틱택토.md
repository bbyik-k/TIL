# React.dev-docs-2-자습서: 틱택토

[REACT 학습하기 > 빠르게 시작하기 > 자습서: 틱택토 게임](https://ko.react.dev/learn/tutorial-tic-tac-toe)

## Goal

- React 기초: 컴포넌트, props, state의 핵심 이해
- 입력에 반응하는 인터랙티브 UI 제작 경험
- “시간 여행(time travel)” 개념을 통한 상태 관리의 유연성 이해

## 자습서 환경 설정

- CodeSandbox에서 _Fork_ 클릭하여 시작점 준비
- 로컬 개발 환경 설정: `npm install` 및 `npm start`로 직접 실습 가능

## 개요

- React의 핵심 개념: 컴포넌트, props, state를 확인
- UI 상에서 직접 Tic-Tac-Toe 게임 형태로 시각화

## 게임 완성하기

- 첫 구현: 클릭된 square가 반응하며 “X” 표시
- 컴포넌트 설계 방식:
  - `Square`는 클릭 상태를 기억 (useState)
  - 코드 복잡해짐 → state를 `Board`로 끌어올림 (State Lifting)
- **Immutability** 적용:
  - `squares.slice()`로 복사하여 상태 관리
  - 변경 불변성을 통해 게임 기록(history) 보존 가능

## 시간 여행 추가하기

- `history`, `currentMove`, `xIsNext` 상태로 여러 게임 상태 저장
- `map()`을 사용하여 이동 버튼 리스트로 출력
  - 각 버튼 클릭 시 `jumpTo(move)`로 해당 시점 상태 복귀
- 시간 여행 기능 구현을 통해 상태 관리의 강력함 체감

## 인사이트

### React 상태 원리

- React에서의 **기억 = state = useState/useReducer**
- 상태 변경 시 렌더링 발생
- React는 상태 비교 시 **얕은 비교(shallow compare)** 를 사용 → 참조 / 메모리 주소 단위 비교

### 불변성 (Immutability)

- React에서 상태 변경의 핵심 원칙은 불변성 유지
- 이전 객체를 직접 수정하지 않고, 복사 후 변경하는 패턴 필수
- 불변성 유지 덕분에:
  - 변경 감지 가능
  - 효율적인 리렌더링 결정 가능
  - time travel과 같은 기능 구현 가능

### 비교 최적화

- React.memo(Component): props 변경 여부를 얕은 비교로 판단
- useMemo, useCallback: deps 배열을 얕은 비교하여 값/함수 재사용 여부 결정
- 클래스 컴포넌트 시절에는 `PureComponent`로 성능 최적화 → 동일 원리
- 얕은 비교의 한계 → 중첩 객체의 경우 별도 깊은 비교 필요

### 깊은 비교 & 복사

- 라이브러리 사용 예:
  - `lodash.isEqual()` → 깊은 비교
  - `lodash.cloneDeep(obj)` → 깊은 복사
- JS 내장 기능:
  - `JSON.parse(JSON.stringify(obj))`
  - `structuredClone(obj)`
- React에서는 **immer** 라이브러리 활용 시 직관적이고 안전한 불변성 유지 가능

> "틱택토 게임 만들기"를 통해, React의 핵심 철학인 **상태 관리 + 불변성 유지 + 얕은 비교 기반의 렌더링 판단**을 실습할 수 있는 챕터였음.
