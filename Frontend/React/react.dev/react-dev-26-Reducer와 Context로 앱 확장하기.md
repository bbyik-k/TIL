# React.dev-docs-26-Reducer와 Context로 앱 확장하기

[REACT 학습하기 > STATE 관리하기 > Reducer와 Context로 앱 확장하기](https://ko.react.dev/learn/scaling-up-with-reducer-and-context)

## Goal

Reducer를 사용하면 컴포넌트의 state 업데이트 로직을 통합할 수 있음.  
Context를 사용하면 다른 컴포넌트들에 정보를 전달할 수 있음.  
Reducer와 context를 함께 사용하여 복잡한 화면의 state를 관리할 수 있다.

- reducer와 context를 결합하는 방법
- state와 dispatch 함수를 prop으로 전달하지 않는 방법
- context와 state 로직을 별도의 파일에서 관리하는 방법

---

## Reducer와 context를 결합하기

- tasks state와 dispatch 함수를 모두 context에 넣어 사용.
  - 이렇게 하면 트리에서 TaskApp 아래에 있는 모든 컴포넌트가 “prop drilling”이라는 반복적인 작업 없이 tasks와 dispatch actions를 읽을 수 있음.

Reducer와 context를 결합하는 방법은 아래와 같다.

1. Context 생성.
2. State와 dispatch 함수를 context에 넣기.
3. 트리 안에서 context를 사용.

### 1단계: Context 생성

- `createContext`를 사용하여 state와 dispatch를 담을 context를 각각 만든다.
- 보통 `TasksContext`, `TasksDispatchContext`처럼 두 개를 나누어 생성한다.

```jsx
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

### 2단계: State와 dispatch 함수를 context에 넣기

- `useReducer`를 이용하여 `state`와 `dispatch`를 생성하고, `Provider`로 트리를 감싼다.
- 이렇게 하면 하위 컴포넌트 어디서든 `state`와 `dispatch`에 접근 가능하다.

```jsx
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // ...
  return (
    <TasksContext value={tasks}>
      <TasksDispatchContext value={dispatch}>...</TasksDispatchContext>
    </TasksContext>
  );
}
```

### 3단계: 트리 안에서 context 사용하기

- 하위 컴포넌트에서 `useContext` Hook을 통해 `state`와 `dispatch`를 가져올 수 있다.
- `TasksProvider`로 감싸져 있으면 props 없이도 접근 가능하다.

```jsx
<TasksContext value={tasks}>
  <TasksDispatchContext value={dispatch}>
    <h1>Day off in Kyoto</h1>
    <AddTask />
    <TaskList />
  </TasksDispatchContext>
</TasksContext>
```

```jsx
export default function TaskList() {
  const tasks = useContext(TasksContext);
  // ...
```

State는 여전히 최상위 TaskApp 컴포넌트에서 useReducer로 관리되고 있음.

- 그러나 이제 `context`를 가져와 트리 아래의 모든 컴포넌트에서 해당 `tasks` 및 `dispatch`를 사용할 수 있음.

## 하나의 파일로 합치기

- `Context`, `Reducer`, `Provider`, `Custom Hook`들을 하나의 파일에 모아 관리할 수 있다.
- 예를 들어 TasksContext.js에서 다음을 모두 export 할 수 있다:

  - `TasksProvider`
  - `useTasks`, `useTasksDispatch` (custom hooks)

- 모든 context와 reducer는 TasksContext.js에 존재.
  - 이렇게 컴포넌트들이 데이터를 어디서 가져오는지가 아닌 무엇을 보여줄 것인지에 집중할 수 있도록 깨끗하게 정리

## 요약

- `Reducer`와 `context`를 결합해서 컴포넌트가 상위 state를 읽고 수정할 수 있도록 할 수 있다.
- State와 dispatch 함수를 하위 컴포넌트들에 제공하는 방법
  1. 두 개의 `contex`t를 만듭니다(각각 `state`와 `dispatch` 함수를 위한 것).
  2. `Reducer`를 사용하는 컴포넌트에 두 `context`를 모두 제공한다.
  3. 하위 컴포넌트들에서 필요한 `context`를 사용한다.
- 더 나아가 하나의 파일로 합쳐서 컴포넌트들을 정리할 수 있다.
  - Context를 제공하는 `TasksProvider` 같은 컴포넌트를 내보낼 수 있다.
  - 바로 사용할 수 있도록 `useTasks`와 `useTasksDispatch` 같은 사용자 Hook을 내보낼 수 있다.
- context-reducer 조합을 앱에 여러 개 만들 수 있다.

## 인사이트

- Context를 단일 전역 저장소처럼 사용하는 것이 아니라, 도메인 단위(예: Tasks, Auth, Theme 등)로 나누어 관리하는 것이 이상적이다.
- Custom Hook(useTasks, useTasksDispatch)을 제공하면 API가 단순해져 팀 협업 시 가독성과 생산성이 향상된다.
