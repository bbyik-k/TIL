# SameValueZero 알고리즘

## SameValueZero 란?

> `SameValueZero`는 **JavaScript의 값 비교 알고리즘** 중 하나로, `Map`, `Set` 등 **해시 기반 자료구조에서 키 또는 값의 동등성 판단**에 사용됨.

- ECMAScript 명세에 정의된 비교 방식
- **엄격 동등 비교(===)** 와 유사하지만, **NaN을 동일하다고 간주**.
- `+0`과 `-0`을 **같은 값**으로 취급.

---

#### 맵이 키를 비교하는 방식

맵은 `SameValueZero` 라 불리는 알고리즘을 사용해 값의 등가 여부를 확인합니다. 이 알고리즘은 일치 연산자 ===와 거의 유사하지만, NaN과 NaN을 같다고 취급하는 것에서 일치 연산자와 차이가 있습니다. 따라서 맵에선 NaN도 키로 쓸 수 있습니다. ([코어 자바스크립트-자료구조와 자료형](https://ko.javascript.info/map-set), 2020)

이 알고리즘은 수정하거나 커스터마이징 하는 것이 불가능합니다.

## 동작 규칙

1. **타입이 다르면** → `false`
2. **타입이 같으면**:
   - **숫자(Number)**:
     - 두 값이 모두 `NaN`이면 `true`
     - `+0`과 `-0`은 `true`
     - 그 외에는 `===` 결과와 동일
   - **문자열(String)**: 내용이 같으면 `true`
   - **불리언(Boolean)**: 값이 같으면 `true`
   - **객체(Object)**: 같은 참조일 때만 `true`
   - **심볼(Symbol)**: 같은 심볼일 때만 `true`

---

## `===`와의 차이점

| 비교 대상      | `===` 결과 | `SameValueZero` 결과 |
| -------------- | ---------- | -------------------- |
| `NaN` vs `NaN` | `false`    | `true`               |
| `+0` vs `-0`   | `true`     | `true`               |
| `1` vs `'1'`   | `false`    | `false`              |
| `{}` vs `{}`   | `false`    | `false`              |

> 차이는 사실상 **NaN 처리 방식**에서만 나타남

---

## 해시 기반 자료구조에서의 활용

### 1. `Map`의 키 비교

```js
const map = new Map();
map.set(NaN, 'value1');
console.log(map.get(NaN)); // 'value1'
```

Map은 내부적으로 키 비교 시 SameValueZero를 사용  
따라서 NaN 키로 값을 넣고 다시 NaN 키로 조회 가능

### 2. `Set` 의 중복 판별

```js
const set = new Set();
set.add(NaN);
set.add(NaN);
console.log(set.size); // 1
```

Set도 같은 원리로 NaN을 동일 값으로 간주하여 중복 저장하지 않음

---

## 의사 코드 (ECMAScript 명세 기반)

```js
function sameValueZero(x, y) {
  if (x === y) {
    // +0과 -0도 true로 처리
    return true;
  }
  // NaN 판별
  return typeof x === 'number' && typeof y === 'number' && isNaN(x) && isNaN(y);
}

sameValueZero(1, 1);
// 1 === 1 → true → return true

sameValueZero(+0, -0);
// +0 === -0 → true → return true

sameValueZero(NaN, NaN);
// NaN === NaN → false
// typeof 둘 다 'number' && isNaN() 둘 다 true → return true

sameValueZero(1, '1');
// 1 === '1' → false
// 숫자-문자열 타입 다름 → return false
```

## 인사이트

`SameValueZero` 는 JS 내부에서 해시 구조의 키/값 비교 로직을 담당하는 알고리즘이다.

- 실무에서 명시적으로 호출하는 경우는 거의 없다.

`NaN` 과 `-0` 처리를 정확히 이해하면,
`Map`, `Set` 사용 시 예상치 못한 키 중복 및 조회 문제를 방지할 수 있다.

---

## TODO

다른 동등성 비교 알고리즘에 대해서도 심호 학습 및 정리할 것

- `==`
- `===`
- `SameValue`

## 레퍼런스

- [ECMAScript® Language Specification – SameValueZero](https://tc39.es/ecma262/#sec-samevaluezero)

- [모던 자바스크립트, 4가지 동등성 비교 알고리즘](https://jake-seo-dev.tistory.com/439)

- [코어 자바스크립트 -> 자료구조와 자료형: 맵과 셋](https://ko.javascript.info/map-set)

- [코어 자바스크립트 -> 자료구조와 자료형: 숫자형](https://ko.javascript.info/number#ref-2163)
