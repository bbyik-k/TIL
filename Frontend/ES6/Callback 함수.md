# Callback 함수란?

**Callback 함수**란 **다른 함수에 인자로 전달되어, 나중에 실행되는 함수**를 의미합니다. 즉, 특정 작업이 완료되거나 특정 조건이 충족될 때 호출되도록 설계된 함수입니다. **JavaScript의 함수는 일급 객체(First-Class Citizen)** 이기 때문에, 다른 함수의 인자로 전달되거나 반환값으로 사용될 수 있습니다. 이 특징을 활용하여 Callback 함수가 사용됩니다.

---

## **Callback 함수의 특징**

1. **인자로 전달**:
   - Callback 함수는 일반적으로 다른 함수의 **인자로 전달**됩니다.
2. **지연 실행**:
   - 즉시 실행되지 않고, 특정 조건이나 작업이 끝난 뒤 실행됩니다.
3. **동기 및 비동기 실행**:
   - 동기 함수 또는 비동기 작업의 결과를 처리할 때 사용됩니다.

---

## **Callback 함수의 동작 원리**

### **1. 함수 인자로 전달**

```javascript
function greet(name, callback) {
  console.log(`Hello, ${name}`);
  callback(); // 전달된 함수를 실행
}

function sayGoodbye() {
  console.log('Goodbye!');
}

greet('Alice', sayGoodbye);
// 출력:
// Hello, Alice
// Goodbye!
```

- 여기서 **`sayGoodbye`** 함수가 `greet` 함수의 인자로 전달되며, `greet` 함수 내부에서 호출됩니다.

---

### **2. 익명 함수로 전달**

익명 함수를 Callback으로 직접 전달할 수도 있습니다.

```javascript
function greet(name, callback) {
  console.log(`Hello, ${name}`);
  callback();
}

greet('Alice', function () {
  console.log('This is a callback function!');
});
// 출력:
// Hello, Alice
// This is a callback function!
```

---

## **Callback 함수의 유형**

### **1. 동기 Callback**

동기적으로 즉시 실행되는 Callback 함수입니다.

```javascript
function calculate(a, b, callback) {
  const result = callback(a, b); // Callback 함수 즉시 실행
  console.log(result);
}

calculate(5, 3, function (x, y) {
  return x + y; // 두 숫자를 더함
});
// 출력: 8
```

- 여기서 Callback은 즉시 실행되어 값을 반환합니다.

---

### **2. 비동기 Callback**

비동기 작업(예: 타이머, HTTP 요청 등)에서 작업이 완료된 후 실행됩니다.

```javascript
console.log('Start');

setTimeout(function () {
  console.log('This is a callback function!');
}, 2000);

console.log('End');
// 출력:
// Start
// End
// This is a callback function! (2초 후 실행)
```

---

## **실제 사용 사례**

### **1. 이벤트 처리**

Callback 함수는 사용자 이벤트 처리에서 자주 사용됩니다.

```javascript
document.getElementById('btn').addEventListener('click', function () {
  console.log('Button clicked!');
});
```

- `addEventListener`의 두 번째 인자로 전달된 함수는 **사용자가 버튼을 클릭했을 때** 실행됩니다.

### **2. 배열 메서드**

배열 메서드(예: `map`, `filter`, `forEach`)에서 Callback 함수는 각 요소에 대해 실행됩니다.

```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(function (num) {
  return num * 2;
});
console.log(doubled); // 출력: [2, 4, 6, 8]
```

### **3. 비동기 작업 (예: HTTP 요청)**

비동기적으로 데이터를 가져온 후, Callback 함수로 결과를 처리합니다.

```javascript
function fetchData(url, callback) {
  setTimeout(() => {
    console.log(`Fetching data from ${url}`);
    callback('Data received');
  }, 1000);
}

fetchData('https://api.example.com', function (data) {
  console.log(data);
});
// 출력:
// Fetching data from https://api.example.com
// Data received
```

---

## **Callback 함수의 단점: 콜백지옥 (Callback Hell)**

Callback 함수가 중첩되면서 코드가 읽기 어렵고 유지보수가 힘들어지는 현상입니다.

### **Callback Hell (콜백지옥) 예제**

```javascript
setTimeout(() => {
  console.log('First Task');
  setTimeout(() => {
    console.log('Second Task');
    setTimeout(() => {
      console.log('Third Task');
    }, 1000);
  }, 1000);
}, 1000);
// 출력:
// First Task
// Second Task
// Third Task (각 작업이 순차적으로 실행)
```

---

## **Callback 함수의 대안**

### **1. `Promise`**

`Promise`는 Callback Hell 문제를 해결하기 위해 등장.

```javascript
const fetchData = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Data received');
  }, 1000);
});

fetchData.then((data) => console.log(data));
```

### **2. `async/await`**

`Promise` 기반 코드를 더 간결하게 작성할 수 있도록 개선.

```javascript
async function fetchData() {
  const data = await new Promise((resolve) => {
    setTimeout(() => {
      resolve('Data received');
    }, 1000);
  });
  console.log(data);
}

fetchData();
```

---

## **결론**

- **Callback 함수**는 JavaScript에서 비동기 작업, 이벤트 처리, 배열 메서드 등 다양한 작업에서 필수적인 개념입니다.
- 하지만 코드가 복잡해지면 **Callback Hell**이 발생할 수 있으며, 이를 해결하기 위해 `Promise`나 `async/await` 같은 현대적인 대안을 사용하는 것이 좋습니다.

---
