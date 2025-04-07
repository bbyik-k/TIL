# JavaScript 동등성 비교 알고리즘 정리

## 동등성 비교 알고리즘이란?

> 동등성 비교 알고리즘은 JavaScript에서 두 값을 비교하여 동일한지 여부를 결정하는 규칙 또는 절차를 정의한 것임.

> JavaScript에는 상황과 목적에 따라 서로 다른 비교 알고리즘이 존재하며, 각기 다른 일관성과 예외 규칙을 가짐.

## JavaScript의 주요 동등성 비교 알고리즘 분류

| 알고리즘                                | 설명                                                 | 사용 예시                     | 주요 특징                                   |
| --------------------------------------- | ---------------------------------------------------- | ----------------------------- | ------------------------------------------- |
| **Abstract Equality Comparison (`==`)** | 서로 다른 타입 간 비교 시 타입 변환을 수행한 뒤 비교 | `1 == '1' // true`            | **암묵적 타입 변환** 발생                   |
| **Strict Equality Comparison (`===`)**  | 타입 변환 없이 값과 타입이 모두 같아야 함            | `1 === '1' // false`          | 더 엄격한 비교, NaN은 자기 자신과 같지 않음 |
| **SameValue**                           | `===`와 유사하지만 `NaN`을 같다고 판단               | `Object.is(NaN, NaN) // true` | -0과 +0을 구분                              |
| **SameValueZero**                       | `SameValue`와 유사하지만 -0과 +0을 동일하게 취급     | `new Set([+0, -0]).size // 1` | `Map`, `Set`, `includes()` 등에 사용        |

## 1. 느슨한 동등 비교 (`==`)

- **타입 변환(Type Coercion)** 이 발생할 수 있음.
- 비교 전 두 값을 동일한 타입으로 변환한 뒤 비교.
- 예시
  ```js
  5 == '5'; // true (문자열 "5"가 숫자 5로 변환됨)
  null == undefined; // true
  ```
- **주의**: 예측 불가능한 결과가 나올 수 있으므로 가급적 사용 지양.
- 여러 케이스가 존재함으로, 이것들을 모두 암기하기 보단, strict 비교 (`===`)를 사용할 것.

---

## 2. 엄격한 동등 비교 (`===`)

- 타입 변환 없이 값과 타입을 모두 비교.
- 예
  ```js
  5 === '5'; // false (타입이 다름)
  5 === 5; // true
  ```
- **권장**: 타입 변환 없이 정확한 비교가 필요할 때 사용.

---

## 3. `SameValue` 알고리즘

- `===`와 유사하지만 **+0과 -0을 구분**하고, **NaN을 같다고 판단**.
- ECMAScript 명세에 정의된 비교 방법.
- 사용 예: `Object.is()` 메서드.
  ```js
  Object.is(+0, -0); // false
  Object.is(NaN, NaN); // true
  ```

---

## 4. `SameValueZero` 알고리즘

- `SameValue`와 거의 동일하나 **+0과 -0을 같다고 판단**.
- `NaN`을 같다고 판단.
- 사용 예: `Map`, `Set`, `Array.prototype.includes()` 내부 비교 로직.
  ```js
  [NaN].includes(NaN); // true
  new Set([NaN]).has(NaN); // true
  ```

---

## 결과 비교 요약 표

| 알고리즘 / 연산자 | +0 vs -0 | NaN vs NaN | 타입 변환 |
| ----------------- | -------- | ---------- | --------- |
| `==`              | true     | false      | O         |
| `===`             | true     | false      | X         |
| SameValue         | false    | true       | X         |
| SameValueZero     | true     | true       | X         |

---

## 인사이트

- **비교 알고리즘의 차이는 의도와 맥락에 따라 선택**해야 한다.  
  예를 들어, 사용자 입력 값 검증 시 `===`를 사용하면 예기치 못한 타입 변환을 **방지**할 수 있고, 해시 기반 자료구조에서는 `SameValueZero`가 내부적으로 활용되어 일관된 키 비교를 보장한다.

- `NaN` 비교나 `-0`과 `+0` 구분 여부처럼 **미묘하지만 중요한 케이스**를 이해하면 디버깅 시 혼란을 줄일 수 있다.

- `==`는 암묵적 타입 변환이 발생하므로 의도하지 않은 true/false 결과를 유발할 수 있어, **명확한 로직에서는 지양**하는 것이 안전하다.

- `SameValue`와 `SameValueZero`의 차이는 **부동소수점 부호 처리** 및 **컬렉션 자료구조 동작**을 이해하는 데 중요하다.

- 실무에서 비교 연산은 값 비교를 외에도 **데이터 무결성, 성능, 보안**까지 영향을 주기 때문에, 내부 동작 원리를 숙지하면 더 안전하고 예측 가능한 코드를 작성할 수 있다.

## 레퍼런스

- [ECMAScript® 2023 Language Specification - Abstract Equality Comparison](https://tc39.es/ecma262/#sec-abstract-equality-comparison)

- [ECMAScript® 2023 Language Specification - Strict Equality Comparison](https://tc39.es/ecma262/#sec-strict-equality-comparison)

- [ECMAScript® 2023 Language Specification - SameValue](https://tc39.es/ecma262/#sec-samevalue)

- [ECMAScript® 2023 Language Specification - SameValueZero](https://tc39.es/ecma262/#sec-samevaluezero)

- [모던 자바스크립트, 4가지 동등성 비교 알고리즘](https://jake-seo-dev.tistory.com/439)
