# Gherkin이란? BDD에서의 역할과 활용

## ✅ Gherkin이란?

**Gherkin**은 **BDD(Behavior-Driven Development, 행위 주도 개발) 기법에서 사용되는 자연어 기반의 테스트 기술**로, 기획자, 개발자, QA가 쉽게 이해할 수 있도록 테스트 시나리오를 작성하는 언어입니다.

- **Given-When-Then 패턴**을 사용하여 **사용자의 행동을 설명하는 문서**를 작성할 수 있음
- **비개발자도 읽을 수 있는 직관적인 문법**을 제공하여, 협업을 원활하게 함
- **Cucumber**와 같은 BDD 프레임워크에서 사용됨
- **요구사항을 명확하게 정의하여 테스트와 개발이 밀접하게 연관될 수 있도록 함**

---

## 📌 Gherkin의 기본 문법

Gherkin은 **"Feature 파일(`.feature`)"** 에 작성되며, 다음과 같은 키워드를 사용합니다.

| 키워드       | 설명                                                |
| ------------ | --------------------------------------------------- |
| `Feature`    | 테스트할 기능을 설명                                |
| `Scenario`   | 특정 상황에서의 테스트 시나리오 정의                |
| `Given`      | 테스트의 초기 상태를 설정 (ex: 사용자 로그인)       |
| `When`       | 사용자의 특정 행동을 정의 (ex: 버튼 클릭)           |
| `Then`       | 예상되는 결과를 정의 (ex: 페이지 이동, 메시지 표시) |
| `And`        | Given, When, Then을 보강하는 추가 조건              |
| `Background` | 모든 시나리오에서 공통적으로 실행될 조건            |

---

## 📌 Gherkin 예제: 로그인 기능

```gherkin
Feature: 사용자 로그인
  사용자는 본인의 계정으로 로그인할 수 있어야 한다.

  Scenario: 정상적인 로그인
    Given 사용자가 로그인 페이지에 접속했을 때
    When 올바른 아이디와 비밀번호를 입력하고 로그인 버튼을 클릭하면
    Then 로그인 성공 메시지를 확인할 수 있다.

  Scenario: 잘못된 비밀번호 입력
    Given 사용자가 로그인 페이지에 접속했을 때
    When 올바른 아이디와 잘못된 비밀번호를 입력하고 로그인 버튼을 클릭하면
    Then 로그인 실패 메시지를 확인할 수 있다.
```

✅ **설명**

- `Feature`: 테스트할 **기능(Feature)** 을 설명
- `Scenario`: 특정 상황에서 실행할 **테스트 케이스** 정의
- `Given`: **초기 상태(환경) 설정** (ex. 로그인 페이지 접속)
- `When`: **사용자의 행동(액션)** (ex. 로그인 버튼 클릭)
- `Then`: **기대 결과 확인** (ex. 성공 메시지 확인)

---

## 📌 Gherkin을 사용하는 이유

✅ **개발자, 기획자, QA 모두가 이해할 수 있는 자연어 기반의 테스트**  
✅ **사용자 중심의 행위를 기준으로 시나리오를 정의** → 요구사항 분석이 쉬워짐  
✅ **자동화 테스트와 연결하여 코드 없이 실행 가능한 테스트 가능**  
✅ **TDD와 BDD의 철학을 반영하여, 테스트 주도 개발이 가능**  
✅ **기획 단계에서 누락될 수 있는 요구사항을 미리 발견 가능**

---

## 📌 Gherkin을 지원하는 BDD 프레임워크

| BDD 프레임워크 | 설명                                           |
| -------------- | ---------------------------------------------- |
| **Cucumber**   | 가장 널리 사용되는 Gherkin 기반 BDD 프레임워크 |
| **SpecFlow**   | .NET 기반 Gherkin 지원 BDD 프레임워크          |
| **Behave**     | Python 기반 Gherkin 지원 프레임워크            |
| **JBehave**    | Java 기반 Gherkin 지원 BDD 프레임워크          |

✅ **특히, Cucumber는 JavaScript, Java, Ruby, Python 등 여러 언어를 지원하여 널리 사용됨.**  
❗️ (Todo): **Cucumber** 사용해보기

---

## 📌 Gherkin을 활용한 실무 적용 사례

### 🔹 **1. 웹 애플리케이션의 사용자 흐름 테스트**

기획자가 작성한 사용자 스토리를 기반으로 시나리오 테스트를 실행할 수 있음.

```gherkin
Feature: 상품 검색 기능
  Scenario: 사용자가 상품을 검색할 때
    Given 사용자가 쇼핑몰 홈페이지에 접속했을 때
    When 검색 창에 "아이폰"을 입력하고 검색 버튼을 클릭하면
    Then "아이폰"과 관련된 상품 목록이 표시된다.
```

### 🔹 **2. API 테스트**

API의 예상 동작을 검증할 때도 사용할 수 있음.

```gherkin
Feature: REST API를 통한 사용자 조회
  Scenario: GET 요청을 통한 사용자 정보 조회
    Given API 서버가 실행 중일 때
    When 클라이언트가 GET "/users/1" 요청을 보내면
    Then 응답 코드가 200이어야 한다.
    And 응답 본문에 "id": 1, "name": "John Doe"가 포함되어야 한다.
```

### 🔹 **3. CI/CD 자동화 테스트**

CI/CD 파이프라인에 Gherkin을 활용하여 배포 전에 자동화된 BDD 테스트를 실행할 수 있음.

```gherkin
Feature: 회원가입 기능 자동 테스트
  Scenario: 사용자가 이메일 중복 체크를 할 때
    Given 사용자가 회원가입 페이지에 접속했을 때
    When 이미 등록된 이메일을 입력하면
    Then "이미 사용 중인 이메일입니다" 메시지가 표시된다.
```

✅ **CI/CD와 결합하면, 코드가 변경될 때마다 자동으로 시나리오 기반의 테스트가 실행되므로 안정성이 향상됨.**

---

## 🚀 결론

- **Gherkin은 BDD에서 사용자의 행위를 자연어 기반으로 테스트할 수 있도록 하는 문법**
- **Given-When-Then 패턴을 사용하여 협업과 문서화를 쉽게 함**
- **Cucumber와 같은 BDD 프레임워크에서 자동화 테스트에 활용 가능**
- **비개발자도 읽을 수 있어 기획 단계에서부터 테스트 설계가 가능**
- **테스트가 단순히 코드 검증이 아니라, 요구사항 명세 및 협업 도구로 활용될 수 있음**

**즉, Gherkin을 사용하면 "사용자가 실제로 하는 행동"을 기반으로 테스트를 작성하고, 기획-개발-QA 간 협업을 강화할 수 있다!**
