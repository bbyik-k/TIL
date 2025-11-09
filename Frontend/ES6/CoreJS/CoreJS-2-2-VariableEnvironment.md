# [Core JS] 2-2. VariableEnvironment

## 요약

- VariableEnvironment는 실행 컨텍스트 생성 시점의 식별자 상태를 스냅샷처럼 보관하는 구조다.
- 실행 흐름을 이해하기보다, 호이스팅과 초기 바인딩의 근거를 설명하는 핵심이다.

## 핵심 내용

### 핵심내용 1. 실행 컨텍스트 생성 시 가장 먼저 채워지는 환경 정보

- 실행 컨텍스트가 생성될 때 자바스크립트 엔진은 필요한 환경 정보를 수집한다.
- 이때 **VariableEnvironment**에는 현재 컨텍스트의 식별자 정보와 외부 환경 참조가 담긴다.
- 이 정보는 이후 코드 실행 중 변경 사항이 반영되지 않는다는 특징을 가진다.

### 핵심내용 2. LexicalEnvironment와 구조는 같지만 역할은 다르다

- VariableEnvironment와 LexicalEnvironment는 내부적으로 동일한 구조를 가진다.
  - environmentRecord
  - outerEnvironmentReference
- 차이는 **변경 반영 여부**다.
  - VariableEnvironment: 초기 상태를 유지
  - LexicalEnvironment: 실행 중 변경 사항을 실시간 반영

### 핵심내용 3. 왜 스냅샷이 필요한가

- 자바스크립트는 실행 전에 식별자 정보를 먼저 확정해야 한다.
- 이를 통해
  - 호이스팅
  - 선언 시점과 실행 시점의 분리
    같은 동작이 가능해진다.
- VariableEnvironment는 이 초기 설계를 보존하는 역할을 맡는다.

## Check Point

- VariableEnvironment는 개발자가 직접 접근하거나 수정할 수 없다.
- “지금 값이 뭐냐”보다 “처음에 어떻게 설계되었느냐”를 설명하는 개념이다.

## 언제 문제가 되는가 / 언제 쓰는가

- 디버깅 중
  - “왜 이 변수는 여기서 undefined일까?”를 설명할 때
- 실행 컨텍스트, 호이스팅, 스코프 체계를 정확히 이해해야 할 때
- 실행 결과가 아니라 **실행 준비 단계**를 추적할 때

## 정리

VariableEnvironment는 코드가 실행되기 전에 자바스크립트 엔진이 그려둔 초기 환경 설계도다.
