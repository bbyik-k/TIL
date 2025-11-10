# [Core JS] 2-3-2. 스코프 체인 & outerEnvironmentReference

## 요약

스코프 체인은 식별자를 찾기 위해 선언 시점의 환경을 따라 위로만 탐색하는 구조이며, 이 연결을 가능하게 하는 것이 `outerEnvironmentReference`다.

## 핵심 내용

### 핵심내용 1. 스코프는 선언 시점에 결정된다

- 자바스크립트에서 스코프는 **어디서 호출됐는지**가 아니라 **어디서 선언됐는지**로 결정된다.
- 함수가 선언될 때, 자신의 LexicalEnvironment가 생성되고 그 시점의 상위 환경을 기억한다.
- 이로 인해 실행 시점이 달라도 참조 가능한 변수 범위는 고정된다.

### 핵심내용 2. outerEnvironmentReference는 상위 환경에 대한 연결이다

- LexicalEnvironment는 environmentRecord와 outerEnvironmentReference로 구성된다.
- outerEnvironmentReference는 **자신이 선언될 당시의 LexicalEnvironment**를 가리킨다.
- 실행 중 식별자를 찾지 못하면, 이 참조를 따라 상위 환경으로 탐색이 이어진다.

### 핵심내용 3. 스코프 체인은 단방향 연결 리스트다

- 스코프 체인은 inner -> outer -> global로 이어지는 단방향 구조다.
- 가장 가까운 스코프부터 순차적으로 탐색하며, 역방향이나 임의 접근은 불가능하다.
- 동일한 이름의 변수가 여러 스코프에 존재하면, 가장 먼저 발견된 값이 사용된다(변수 은닉).

## Check Point

- 스코프는 실행 순서가 아니라 선언 구조로 결정된다.
- outerEnvironmentReference는 호출 시점이 아닌 선언 시점을 기준으로 설정된다.
- 스코프 체인은 위로만 탐색되며 가장 가까운 식별자가 우선된다.

## 언제 문제가 되는가 / 언제 쓰는가

- 디버깅 시 예상과 다른 변수가 참조될 때
- 중첩 함수에서 외부 변수를 참조할 때
- React에서 클로저로 인해 오래된 state를 참조하는 버그를 이해할 때

## 정리

스코프 체인은 선언 시점의 환경을 따라 위로만 탐색하는 구조이며, 그 연결 고리가 바로 outerEnvironmentReference다.
