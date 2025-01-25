# TanStack Query

- 공식 사이트: [TanStack Query](https://tanstack.com/query/latest)
- DOCS: [Overview | TanStack Query React Docs](https://tanstack.com/query/latest/docs/framework/react/overview)

- TanStack의 의미

  - Tan: 창립자 Tanner Linsley의 이름에서 따온 것.
  - Stack: 다양한 라이브러리들이 함께 제공된다는 의미.
  - TanStack은 단일 라이브러리가 아니라 "React Query", "TanStack Table", "TanStack Router" 등 여러 라이브러리를 포함한 프로젝트 스택.

- 사용 목적
  - 기존 Hook 의 문제
    - Hook 은 값의 재사용이 아닌, 로직의 재사용을 위한 것
    - 따라서\!
      - 값의 Cache가 되지 않음. \- 새롭게 data를 계속 받아옴.
      - Retry 기능의 부재: loading, error 에 대한 처리는 하지만, 네트워크 통신 실패 시 다시 재시도 하는 기능의 결여. \- 개발자(본인)가 구현해두지 않음.
  - React query (TanStack query) 는 이러한 문제들을 해결.
    - loading, error, success 처리
    - 같은 data caching 처리
    - global 상태 관리
    - fail 시 일정 시간 후 retry 기능 제공
    - 네트워크 통신, 비동기적 데이터 관리 시 유용하게 이용 가능
