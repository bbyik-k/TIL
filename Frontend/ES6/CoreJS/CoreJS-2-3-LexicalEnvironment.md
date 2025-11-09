# [Core JS] 2-3. LexicalEnvironment

## 요약

LexicalEnvironment는 **식별자와 스코프 관계를 실시간으로 관리하는 실행 컨텍스트의 핵심 구조**이며,  
자바스크립트에서 스코프 체인과 호이스팅이 동작하는 근본적인 이유다.

> `Lexical`: 어휘의  
> 어휘점 범위: 함수가 호출되어지는 위치가 아닌,  
> 코드가 작성된 위치(선언된 위치)에 따라 변수의 유효 범위가 결정되는 것을 의미

## 핵심 내용

### 핵심내용 1. LexicalEnvironment란 무엇인가

- LexicalEnvironment는 **현재 실행 컨텍스트에서 유효한 식별자들과 그 외부 환경에 대한 참조를 담는 객체 구조**다.
- 실행 컨텍스트가 생성될 때 함께 만들어지며, 코드 실행 중 **변경 사항이 즉시 반영**된다.
- 내부적으로는 두 가지로 구성된다.
  - `environmentRecord` : 현재 스코프의 식별자 정보
  - `outerEnvironmentReference` : 상위 스코프(LexicalEnvironment)에 대한 참조

### 핵심내용 2. VariableEnvironment와의 차이

- 초기 생성 시에는 LexicalEnvironment와 VariableEnvironment의 내용은 동일하다.
- 하지만 **VariableEnvironment는 생성 시점의 스냅샷**,  
  **LexicalEnvironment는 실행 중 변경을 계속 반영**한다.
- 이 차이로 인해 실제 코드 실행에서는 LexicalEnvironment가 주로 사용된다.

### 핵심내용 3. 스코프 체인의 실체

- 스코프 체인은 별도의 개념이 아니라  
  **LexicalEnvironment의 outerEnvironmentReference를 따라 탐색하는 과정**이다.
- 식별자를 찾을 때
  1. 현재 environmentRecord에서 탐색
  2. 없으면 outerEnvironmentReference로 이동
  3. 전역 LexicalEnvironment까지 반복
- 이 구조 덕분에 **렉시컬 스코프(선언 위치 기준 스코프)** 가 구현된다.

## Check Point

- LexicalEnvironment는 “정적”이 아니라 **선언 위치를 기준으로 한 연결 구조**
- 스코프 체인은 자료구조가 아니라 **탐색 규칙**
- 실행 중 값 변화는 LexicalEnvironment에만 반영됨

## 언제 문제가 되는가 / 언제 쓰는가

- 디버깅 시
  - 같은 이름의 변수가 있는데 값이 다르게 보일 때
  - 클로저 내부에서 예상과 다른 값이 참조될 때
- React 함수 컴포넌트에서
  - 렌더링마다 생성되는 스코프와 상태 캡처 현상을 이해할 때
- 호이스팅, TDZ, 클로저를 정확히 이해해야 하는 상황 전반

## 정리

LexicalEnvironment는 **“지금 이 코드가 어떤 변수들을, 어떤 순서로, 어디까지 볼 수 있는지”를 결정하는 구조**.
