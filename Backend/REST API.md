# 📌 REST API 개념 정리

## 🔹 REST API란?

REST API는 **Representational State Transfer**의 약자로, **웹에서 자원을 효율적으로 관리하기 위한 아키텍처 스타일**입니다. REST API는 HTTP 프로토콜을 기반으로 동작하며, 클라이언트와 서버 간의 통신을 간결하고 일관성 있게 설계할 수 있도록 도와줍니다.

---

## 🔹 REST vs RESTful

- **REST**: 자원을 표현하는 방식과 상태를 전송하는 구조적인 스타일
- **RESTful**: REST의 원칙을 올바르게 준수한 API 디자인

### ✅ REST의 주요 원칙

1. **클라이언트-서버 구조**: 클라이언트와 서버를 분리하여 독립적인 개발이 가능하게 함.
2. **무상태성(Stateless)**: 각 요청은 독립적이며, 서버는 이전 요청 상태를 저장하지 않음.
3. **캐시 처리 가능(Cacheable)**: HTTP 캐싱을 활용하여 성능 최적화.
4. **계층화된 시스템(Layered System)**: API 서버, 데이터베이스, 인증 서버 등을 계층적으로 설계 가능.
5. **일관된 인터페이스(Uniform Interface)**: HTTP 메서드(GET, POST 등)와 URI를 이용하여 자원에 접근.
6. **Code on Demand (선택적)**: 필요하면 클라이언트가 실행할 수 있는 코드를 서버가 전달할 수도 있음.

---

## 🔹 REST API의 HTTP 메서드

REST API에서는 자원(Resource)에 대한 작업을 HTTP 메서드로 정의합니다.

| 메서드   | 기능                           | 예시              |
| -------- | ------------------------------ | ----------------- |
| `GET`    | 데이터를 조회 (Read)           | `GET /users/1`    |
| `POST`   | 새로운 데이터 생성 (Create)    | `POST /users`     |
| `PUT`    | 기존 데이터 전체 수정 (Update) | `PUT /users/1`    |
| `PATCH`  | 기존 데이터 부분 수정 (Update) | `PATCH /users/1`  |
| `DELETE` | 데이터 삭제 (Delete)           | `DELETE /users/1` |

### ✅ `GET` vs `POST`

- **`GET`**: 데이터를 가져올 때 사용, 요청의 데이터가 URL에 포함됨.
- **`POST`**: 데이터를 생성할 때 사용, 요청의 데이터가 **본문(Body)**에 포함됨.

```plaintext
GET /users?id=1  // URL에 데이터 포함
POST /users { "name": "철수" }  // 본문(Body)에 데이터 포함
```

📌 **GET은 멱등성(idempotent)이 있지만, POST는 멱등하지 않음!**

- GET 요청을 여러 번 보내도 같은 응답을 받음.
- POST 요청을 여러 번 보내면 데이터가 중복 생성될 수 있음.

---

## 🔹 URI vs URL

### ✅ URI (Uniform Resource Identifier)

- **자원을 식별하는 전체적인 개념**
- 모든 URL은 URI이지만, 모든 URI가 URL은 아님.

### ✅ URL (Uniform Resource Locator)

- **자원의 위치(주소)를 포함하는 URI**
- 특정 리소스의 접근 경로를 포함함.

📌 **예제 비교**

```plaintext
URI: https://example.com/users/1
URL: https://example.com/users/1 (URI이면서 URL)
URN: users/1 (자원의 이름만 가리킴, URL이 아님)
```

---

## 📌 요약

- **REST API**는 자원(Resource)을 HTTP 메서드를 이용해 조작하는 방식.
- **RESTful API**는 REST 원칙을 잘 지킨 API 디자인을 의미.
- **GET과 POST의 차이**: GET은 데이터를 조회, POST는 데이터를 생성.
- **URI vs URL**: URL은 URI의 한 종류로, 특정 위치를 포함함.

REST API를 올바르게 설계하면 유지보수성과 확장성이 뛰어난 웹 서비스를 구축할 수 있습니다! 🚀
