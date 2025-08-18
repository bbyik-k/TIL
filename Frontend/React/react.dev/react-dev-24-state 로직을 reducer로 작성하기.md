# React.dev-docs-24-state 로직을 reducer로 작성하기

[REACT 학습하기 > STATE 관리하기 > state 로직을 reducer로 작성하기](https://ko.react.dev/learn/preserving-and-resetting-state)

## Goal

한 컴포넌트에서 state 업데이트가 여러 이벤트 핸들러로 분산되는 경우에 사용.  
state를 업데이트하는 모든 로직을 reducer를 사용해 컴포넌트 외부의 단일 함수로 통합해 관리.

- reducer 함수란 무엇인가
- `useState`에서 `useReducer`로 리팩토링 하는 방법
- reducer를 언제 사용할 수 있는지
- reducer를 잘 작성하는 방법

## reducer를 사용하여 state 로직 통합하기

### 1단계: state를 설정하는 것에서 action을 dispatch 함수로 전달하는 것으로 바꾸기

### 1단계: state를 직접 설정하는 것에서 action을 dispatch 함수로 전달하는 것으로 바꾸기

- `setState`는 곧바로 “값을 이렇게 바꿔라”를 지정하지만, reducer 패턴에서는 **무슨 일이 일어났는지**를 설명하는 `action`을 dispatch한다.
- 예: `setTasks([...tasks, newTask])` 대신 아래처럼 작성한다.

  ```jsx
  dispatch({
    type: 'added',
    id: nextId++,
    text: text,
  });
  ```

> **중요합니다!**
>
> action 객체는 단순한 JS 객체이며, 보통 `type` 문자열과 추가 정보를 담는다.
>
> `type`은 “어떤 일이 일어났는가”를 설명하는 이름을 써야 한다. (`added`, `deleted`, `reset_form` 등)

### 2단계: reducer 함수 작성하기

- reducer 함수는 현재 state와 action을 입력으로 받아 다음 state를 반환한다.
- 반드시 순수 함수로 작성해야 한다. 같은 입력 → 같은 출력.
- 보통 `switch`문을 사용하여 action type에 따라 분기한다.

```jsx
export default function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    case 'changed': {
      return tasks.map((t) => (t.id === action.task.id ? action.task : t));
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

> **중요합니다!**
>
> reducer 내부에서 `if/else` 문도 가능하지만, 보통 `switch`문을 사용하면 가독성이 좋고 유지보수에 유리하다.

> **왜 reducer라고 부르게 되었을까요?**
>
> 배열의 reduce 함수와 유사하게, 일련의 action들을 받아 state를 “누적”해서 최종 상태를 만든다는 의미.

### 3단계: 컴포넌트에서 reducer 사용하기

- 컴포넌트에서 `useReducer`를 불러와 reducer 함수와 초기값을 연결한다.

```jsx
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

- 이제 이벤트 핸들러에서는 `setTasks` 대신 `dispatch`를 호출한다.
- dispatch가 action을 reducer로 전달 → reducer가 새로운 state를 반환 → React가 state를 갱신.

## `useState`와 `useReducer` 비교하기

**코드 크기**

- 일반적으로 useState를 사용하면, 미리 작성해야 하는 코드가 줄어듦.
- useReducer를 사용하면 reducer 함수, action을 전달하는 부분 둘 다 작성해야 함.
- 하지만 여러 이벤트 핸들러에서 비슷한 방식으로 state를 업데이트 하는 경우, useReducer를 사용하면 코드의 양을 줄이는 데 도움

**가독성**

- useState로 간단한 state를 업데이트 하는 경우 가독성이 좋은 편.
  - 더 복잡한 구조의 state를 다루게 되면, 컴포넌트의 코드 양이 더 많아져 한눈에 읽기 어려워짐.
- 이 경우 useReducer를 사용하면, 업데이트 로직이 어떻게 동작하는지와 이벤트 핸들러를 통해서 무엇이 발생했는지 구현한 부분을 명확하게 구분할 수 있음.

**디버깅**

- useState를 사용하며 버그를 발견했을 때, 왜, 어디서 state가 잘 못 설정 됐는지 찾기 어려움.
- useReducer를 사용하면, 콘솔 로그를 reducer 함수에 추가하여 state가 업데이트 되는 모든 부분과, 왜 해당 버그가 발생했는지(어떤 action으로 인한 것인지)를 확인할 수 있음.
  - 각 action이 올바르게 작성되어 있다면, 버그를 발생시킨 부분이 reducer 로직 자체에 있다는 것을 알 수 있음.
  - 그렇지만, useState를 사용하는 경우보다 더 많은 코드를 단계별로 실행해서 디버깅 해야 하는 점이 있기도 함.

**테스팅**

- reducer는 컴포넌트에 의존하지 않는 순수 함수. 따라서 reducer를 독립적으로 분리해서 내보내거나, 테스트 할 수 있음.
  - 일반적으로 더 현실적인 환경에서 컴포넌트를 테스트하는 것이 좋지만, 복잡한 state를 업데이트하는 로직의 경우 reducer가 특정 초기 state 및 action에 대해 특정 state를 반환한다고 생각하고 테스트하는 것이 유용할 수 있음.

**reducer 사용 권장**

1. 일부 컴포넌트에서 잘못된 방식으로 state를 업데이트 하는 것으로 인한 버그가 자주 발생하거나
2. 해당 코드에 더 많은 구조를 도입하고 싶다면 reducer 사용을 권장

- 이때 모든 부분에 reducer를 적용하지 않아도 됨.
  - useState와 useReducer를 마음대로 섞고 매치 가능
  - 같은 컴포넌트 안에서도 섞어 사용 가능

## reducer 잘 작성하기

reducer 작성 시 명심할 두 가지 팁

- **Reducer는 반드시 순수해야 한다.**
  - state 업데이트 함수와 비슷하게, reducer는 렌더링 중에 실행됨.
    - action은 다음 렌더링까지 대기함.
  - 따라서 reducer는 반드시 순수함수로 작성되어야 함.
    - 입력 값이 같다면 -> 결과 값도 항상 같아야 함.
  - 요청을 보내거나, timeout을 스케쥴링 하거나, 사이드 이펙트를 수행해서는 안 됨.
  - reducer는 객체와 배열을 변경하지 않고 업데이트 해야함.
- **각 action은 데이터 안에서 여러 변경들이 있더라도 하나의 사용자 상호작용을 설명해야 한다.**
  - ex) 사용자가 reducer가 관리하는 개 필드가 있는 양식에서 ‘재설정’을 누른 경우, 5개의 개별 ‘`set_field`’ `action` 보다는 -> 하나의 ‘`resest_form`’ `action`을 전송하는 것이 더 합리적.
  - 모든 action을 reducer에 기록하면, 어떤 상호작용이나 응답이 어떤 순서로 일어났는지 재구성할 수 있을 만큼 로그가 명확해야 함.
    - 디버깅 용이성
- action 객체의 type은 “state가 어떻게 변경되길 원하는지”보다, “**_사용자가 무엇을 했는지_**”를 이상적으로 설명해야 한다는 점을 명심할 것.
  - 이렇게 하면 이후에 기능을 추가하기 훨씬 더 수월해짐.

## Immer로 간결한 reducer 작성하기

- Immer 라이브러리를 사용하면 reducer를 더 간결하게 작성할 수 있음.
  - Immer는 draft state를 직접 변경하는 코드처럼 작성할 수 있게 해주며, 내부적으로는 불변성을 유지한다.
- `useImmerReducer` 사용

```jsx
import { useImmerReducer } from 'use-immer';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added':
      draft.push({ id: action.id, text: action.text, done: false });
      break;
    case 'deleted':
      return draft.filter((t) => t.id !== action.id);
    case 'changed':
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    default:
      throw Error('Unknown action: ' + action.type);
  }
}
```

## 요약

- `useState`에서 `useReducer`로 변환하려면
  1. 이벤트 핸들러에서 action을 전달한다.
  2. 주어진 state와 action에 대해 다음 state를 반환하는 `reducer 함수`를 작성한다.
  3. `useState`를 `useReducer`로 바꾼다.
- reducer를 사용하면 코드를 조금 더 작성해야 하지만, 디버깅과 테스트에 도움이 됨.
- reducer는 반드시 순수해야 함.
- 각 action은 단일 사용자 상호작용을 설명해야 함.
- 객체와 배열을 변경하는 스타일로 reducer를 작성하려면, Immer 라이브러리를 사용할 것.

## 인사이트

- 컴포넌트의 복잡성이 커질수록 useReducer의 장점이 부각됨.
  - 이벤트 핸들러마다 흩어진 로직을 하나의 reducer로 모아 관리의 수월.
- action 기반 사고방식은 “어떤 값으로 바꿀까?”가 아니라 “**사용자가 어떤 일을 했는가?**”에 집중할 수 있어, 로직의 의미 전달력이 커진다.
- reducer는 순수 함수이므로 독립적인 테스트가 가능하다. 이는 컴포넌트 테스트보다 디버깅/품질 관리에 큰 장점을 제공한다.
