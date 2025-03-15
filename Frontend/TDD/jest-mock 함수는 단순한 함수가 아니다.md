# Jest Mock 함수의 동작 방식과 활용

## ✅ Jest에서 `jest.fn()` (Mock Function) 동작 방식

`jest.fn()`은 Jest에서 **테스트용으로 가짜(Mock) 함수를 만드는 기능**을 제공합니다.  
이 가짜 함수(Mock Function)를 사용하면 **어떤 함수가 호출되었는지, 몇 번 호출되었는지, 어떤 인자가 전달되었는지** 등을 확인할 수 있습니다.

---

## 📌 `jest.fn()`을 사용하는 이유

테스트에서 `check()` 함수를 실행한 후, Mock 함수가 올바르게 호출되었는지를 검증하는 방식입니다.

```js
// given: Mock 함수(onSuccess, onFail)를 준비
let onSuccess = jest.fn();
let onFail = jest.fn();

// when: check 함수를 실행
check(() => false, onSuccess, onFail);

// then: onFail이 호출되었는지 확인
expect(onSuccess).toHaveBeenCalledTimes(0);
expect(onFail).toHaveBeenCalledWith('no');
expect(onFail).toHaveBeenCalledTimes(1);
```

### ✅ 테스트 흐름

1. **given:** 테스트할 환경을 설정 (`jest.fn()`으로 Mock 생성)
2. **when:** 테스트 대상 함수를 실행 (`check()` 호출)
3. **then:** 실행 결과를 검증 (`expect()` 사용)

이것이 **BDD(Behavior Driven Development) 스타일의 테스트 방식**입니다.

---

## 📌 `jest.fn()`은 단순한 함수가 아니다!

### 🔹 실제 데이터 구조 예시

```js
const mockFn = jest.fn();

mockFn('hello');
mockFn('world');

console.log(mockFn.mock.calls);
/* 출력:
[
  ['hello'],
  ['world']
]
*/
```

✅ **이런 데이터를 기반으로 `toHaveBeenCalledWith()`, `toHaveBeenCalledTimes()` 같은 Jest의 matcher들이 동작하는 것!**

---

## 📌 Jest Mock 함수의 실무 활용 예시

### 🔹 1. **비동기 API 요청을 Mock 처리**

```js
import axios from 'axios';

jest.mock('axios'); // axios를 Mocking

test('API 요청이 정상적으로 처리되는지 확인', async () => {
  axios.get.mockResolvedValue({ data: { message: 'success' } });

  const response = await axios.get('/api/data');

  expect(response.data).toEqual({ message: 'success' });
  expect(axios.get).toHaveBeenCalledTimes(1);
  expect(axios.get).toHaveBeenCalledWith('/api/data');
});
```

---

### 🔹 2. **이벤트 핸들러가 정상적으로 호출되는지 확인**

```js
test('버튼 클릭 시 onClick 핸들러가 호출되는지 테스트', () => {
  const onClickMock = jest.fn();

  const button = document.createElement('button');
  button.addEventListener('click', onClickMock);

  button.click(); // 클릭 이벤트 발생

  expect(onClickMock).toHaveBeenCalledTimes(1);
});
```

---

## 📌 Jest Mock 함수가 제공하는 주요 메서드

| 메서드                                   | 설명                                       |
| ---------------------------------------- | ------------------------------------------ |
| `.mock.calls.length`                     | 호출된 횟수를 반환                         |
| `.mock.calls[index]`                     | 특정 호출에서 전달된 인자 확인             |
| `.toHaveBeenCalled()`                    | 함수가 한 번이라도 호출되었는지 확인       |
| `.toHaveBeenCalledTimes(n)`              | 함수가 정확히 `n`번 호출되었는지 확인      |
| `.toHaveBeenCalledWith(arg1, arg2, ...)` | 특정 인자로 호출되었는지 확인              |
| `.mockReturnValue(value)`                | 호출될 때 특정 값을 반환하도록 설정        |
| `.mockResolvedValue(value)`              | `Promise.resolve(value)`를 반환하도록 설정 |
| `.mockRejectedValue(error)`              | `Promise.reject(error)`를 반환하도록 설정  |

---

## ✅ 최종 정리

✅ **Mock 함수(`jest.fn()`)의 역할**

- 실제 함수처럼 동작하지만, 호출 횟수와 전달된 인자를 추적할 수 있는 **"객체"**다.
- `toHaveBeenCalledTimes(n)`, `toHaveBeenCalledWith(args)` 등을 활용하여 검증 가능.

✅ **BDD 스타일 테스트**

1. **Given** → 테스트 환경 설정 (`jest.fn()`을 이용해 Mock 함수 생성)
2. **When** → 실제 코드 실행 (`check()` 호출)
3. **Then** → 실행 결과 검증 (`expect()`로 확인)

✅ **실무에서 Mock 함수 활용 예시**

- API 호출 테스트 (`jest.mock('axios')`)
- 이벤트 핸들러 테스트 (`jest.fn()`을 클릭 이벤트에 연결)

---

## 🎯 **결론**

Jest에서 `jest.fn()`을 사용한 Mock 함수는 단순한 가짜 함수가 아니라 **호출 정보를 추적하는 객체**입니다.  
테스트에서는 **"결과를 검증"하는 것이 중요하므로**, 실행 자체는 `expect()` 안에서 하지 않고, **미리 실행된 결과를 검증하는 방식**을 많이 사용합니다.
