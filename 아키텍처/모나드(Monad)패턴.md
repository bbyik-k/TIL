# 🎁 모나드(Monad) 패턴

## 모나드란?

> 값을 안전하게 감싸고, 연산을 체이닝하며 이어갈 수 있도록 하는 함수형 프로그래밍 패턴

"값이 없으면 스킵하고, 있으면 연산을 이어가자" 또는 "실패해도 프로그램을 죽이지 않고 흐름을 안전하게 유지하자"는 목표에서 시작됨.

---

## 모나드의 구조

모나드는 다음 세 가지 구조를 포함한다.

1. **값을 감싼다 (wrap)**: 값을 모나드 컨테이너 안에 넣음 (`Maybe(value)` 등)
2. **map**: 값이 있으면 연산을 적용하고, 없으면 아무것도 하지 않음
3. **flatMap / bind**: 중첩된 모나드를 평탄화하여 체이닝 연산을 가능하게 함.

---

## 대표 예: Maybe class

- 특징: 값이 존재할 수도, 존재하지 않을 수도 있다.
  - 값의 예외를 생각하며 만들어짐.

```ts
class Maybe<T> {
  constructor(private value: T | null | undefined) {}

  static of<T>(value: T | null | undefined) {
    return new Maybe(value);
  }

  map<U>(fn: (val: T) => U): Maybe<U> {
    if (this.value == null) return new Maybe<U>(null);
    return new Maybe<U>(fn(this.value));
  }

  getOrElse(defaultValue: T): T {
    return this.value == null ? defaultValue : this.value;
  }

  flatMap<U>(fn: (val: T) => Maybe<U>): Maybe<U> {
    if (this.value == null) return new Maybe<U>(null);
    return fn(this.value); // flatMap은 함수가 또 모나드를 반환한다고 가정
  }
}

const result = Maybe.of(getUser())
  .map((user) => user.profile)
  .map((profile) => profile.name)
  .getOrElse('이름 없음');
```

### 필수 모나드 인터페이스

`unit`, `map`, `flatMap (bind)`

- `Maybe.of()`는 값을 감싸고
- `map()`은 중간 연산을 안전하게 이어가며
- `getOrElse()`는 최종 기본값을 반환함 - 보조메서드

---

### JS에서 확인할 수 있는 모나드: Promise

```ts
Promise.resolve(1)
  .then((x) => x + 1)
  .then((x) => x * 2); // 결과: 4
```

- `Promise`도 값을 감싸고 (`resolve`) → map처럼 `.then()`으로 연산을 이어감
- 실패 (`reject`) 시 `.catch()`로 흐름을 안정적으로 처리 가능
- 체이닝을 가능하게 하여, 콜백지옥(callback hell)로 부터 해방
- 이 외에도 `Array.flatMap()` 메서드가 모나드의 형태를 띄고 있다.
  - 일반 `Array.map()` 메서드는 모나드 보단 `Functor`라 볼 수 있음.

## 모나드 사용시의 법칙

모나드 사용시에는 수학적인 대수적 법칙(모나드 법칙)이 적용 된다.

### 좌항 단위 법칙 (Left Identity)

`unit(x).flatMap(f) ≡ f(x)`

- 값을 모나드로 감싼 후 flatMap으로 함수를 적용한 결과는, 그냥 그 함수에 직접 적용한 것과 같다.

예시

```ts
const f = (x: number) => Maybe.of(x + 1);

Maybe.of(3).flatMap(f); // ⟺ f(3) → 둘은 동일해야 한다
```

### 우항 단위 법칙 (Right Identity)

`m.flatMap(unit) ≡ m`

- 모나드에 unit (wrap 함수)을 flatMap으로 적용하면 원래의 모나드와 같아야 한다.

예시

```ts
const m = Maybe.of(3);
m.flatMap(Maybe.of) === m; // true
```

### 결합 법칙 (Associativity)

`m.flatMap(f).flatMap(g) ≡ m.flatMap(x => f(x).flatMap(g))`

- 체이닝 순서를 바꿔도 결과는 같아야 한다.

예시

```ts
const f = (x: number) => Maybe.of(x + 1);
const g = (x: number) => Maybe.of(x * 2);
const m = Maybe.of(3);

// 두 방식은 같아야 한다
const a = m.flatMap(f).flatMap(g);
const b = m.flatMap((x) => f(x).flatMap(g));
```

> 예측 가능한 동작 보장

> 컴포지션 가능: 체이닝이 자유롭고, 구조를 깨지 않음

> 에러 처리, 비동기, null-safe 코드 등에서 안정성 향상

## 정리

| 요소               | 설명                                      |
| ------------------ | ----------------------------------------- |
| `Maybe`, `Option`  | 값이 없을 수도 있을 때 null-safe하게 처리 |
| `Promise`          | 비동기 연산을 안전하게 이어감             |
| `Either`, `Result` | 실패/성공을 구분하고 분기 처리 가능       |

모나드는 값을 감싸고, 그 안에서 안전하게 연산을 이어갈 수 있도록 보장하는 디자인 패턴이다. 그리고 이후 주어지는 함수를 `map` 또는 `flatMap` 처리하여, 다른 컨테이너를 반환하는 기능을 가진 마법 컨테이너(상자)이다. 이를 통해 **모나드는 "안전하고 예측 가능한 연산 흐름"을 만들기 위한 패턴이다.**

---

## 인사이트

- 모나드는 개념보다 **패턴으로 받아들이는 것이 훨씬 이해가 빠름**
  - 모나드의 수학적 개념은 매우 어렵다.
  - 패턴 자체의 개념도 어려워서, 여러 자료들과 영상을 참고함.
- JS에서도 이미 `Promise`, `Array.flatMap`, `Optional chaining`, `nullish 연산자` 등 모나드 스타일이 널리 쓰이고 있음
- 값이 유효하지 않을 수도 있는 연산 흐름을 **안정적으로 연결하고 싶을 때** 모나드 패턴이 유용함

## 레퍼런스

- [모나드(Monad), 니가 대체 뭔데?](https://velog.io/@yesoryeseul/%EB%AA%A8%EB%82%98%EB%93%9CMonad-%EB%8B%88%EA%B0%80-%EB%8C%80%EC%B2%B4-%EB%AD%94%EB%8D%B0)
- [2022-05-13 함수형 프로그래밍 & 모나드](https://saengmotmi.netlify.app/javascript-study/2022-05-13-monad/#1-%EC%A0%95%EC%9D%98%EB%A5%BC-%EB%B0%9C%EC%B7%8C%ED%95%98%EB%8A%94-%EA%B2%83%EB%B6%80%ED%84%B0-%EC%8B%9C%EC%9E%91%ED%95%B4%EB%B3%B4%EC%9E%90)
- [모나드(Monad)는 사실 쉽습니다. - 함수형 프로그래밍 끝판왕 깨기](https://www.youtube.com/watch?v=_k7102uGOco)
- [모나드란 무엇인가?](https://velog.io/@jeunghun2/Design-Pattern-%EB%AA%A8%EB%82%98%EB%93%9C%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
- [낙타의 블로그 :: 함수형 프로그래밍 in JS (4) - Functor, Maybe](https://nakta.dev/fp-in-js-4-functor-maybe)
- [모나드 예제 코드](https://yalco.notion.site/Monad-f6054c8685f14a73a4a6853cd11f4431)
