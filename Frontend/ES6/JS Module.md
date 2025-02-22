# 📌 JavaScript 모듈 (Module)

## 🔹 모듈이란?

JavaScript에서 모듈(Module)은 **파일 내의 코드를 독립적으로 관리**할 수 있도록 하는 기능입니다. 모듈을 사용하면 **전역 범위(global scope) 오염을 방지**하고, **재사용 가능하고 유지보수하기 쉬운 코드**를 작성할 수 있습니다.

## 🔹 모듈화를 하지 않으면?

- 모든 변수와 함수가 **전역 객체(window/globalThis)**에 등록됨
- 여러 개의 파일에서 동일한 함수 또는 변수를 정의하면 **충돌 발생**
- 코드가 길어질수록 유지보수가 어려워짐

```javascript
// script.js (모듈화 X)
function greet() {
  console.log('안녕하세요!');
}

function greet() {
  console.log('Hello!');
}

greet(); // "Hello!" (함수가 덮어씌워짐)
```

📌 **문제점:** 같은 이름의 `greet` 함수가 여러 개 존재할 경우 마지막으로 등록된 함수가 실행됨.

---

## 🔹 ES6 모듈 사용하기

ES6에서는 `import` / `export` 키워드를 사용하여 모듈을 관리할 수 있습니다.

### ✅ 모듈 내보내기 (`export`)

모듈 파일에서 필요한 변수나 함수를 `export` 키워드를 사용하여 내보낼 수 있습니다.

```javascript
// math.js
export function add(a, b) {
  return a + b;
}

export const PI = 3.1415;
```

### ✅ 모듈 가져오기 (`import`)

다른 파일에서 `import` 키워드를 사용하여 모듈을 가져올 수 있습니다.

```javascript
// main.js
import { add, PI } from './math.js';

console.log(add(2, 3)); // 5
console.log(PI); // 3.1415
```

📌 **장점:**

- `math.js`에서 `add`와 `PI`만 가져오기 때문에 **불필요한 코드가 실행되지 않음**
- 전역 범위를 오염시키지 않으며, 충돌이 방지됨

---

## 🔹 Default Export

`export default`를 사용하면 모듈에서 단 하나의 값을 기본으로 내보낼 수 있습니다.

```javascript
// logger.js
export default function log(message) {
  console.log('[LOG]:', message);
}
```

```javascript
// app.js
import log from './logger.js';

log('모듈 시스템을 배우는 중!');
// 출력: [LOG]: 모듈 시스템을 배우는 중!
```

📌 **Default Export의 특징:**

- `import` 시 중괄호 `{}` 없이 가져올 수 있음
- 파일에서 **단 하나의 값만 내보낼 때 적합**

---

## 🔹 브라우저에서 모듈 사용하기

HTML 파일에서 `<script>` 태그에 `type="module"` 속성을 추가하면 브라우저에서 모듈을 인식할 수 있습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="module" src="main.js"></script>
  </head>
  <body>
    <h1>JavaScript 모듈 예제</h1>
  </body>
</html>
```

📌 **주의사항:**

- 모듈은 **자동으로 strict mode**로 실행됨
- `type="module"`을 사용한 스크립트는 **지연 실행(defer)됨**
- 같은 모듈을 여러 번 가져와도 **한 번만 로드됨 (캐싱됨)**

---

## 🔹 Node.js에서 모듈 사용하기

Node.js 환경에서는 **CommonJS 방식**(`require`, `module.exports`) 또는 **ESM(ECMAScript Module) 방식**을 사용할 수 있습니다.

### ✅ CommonJS 방식 (`require` / `module.exports`)

```javascript
// util.js
module.exports = {
  greet(name) {
    return `안녕하세요, ${name}!`;
  },
};
```

```javascript
// index.js
const util = require('./util.js');

console.log(util.greet('철수')); // "안녕하세요, 철수!"
```

### ✅ ES 모듈 방식 (Node.js 14+ 지원)

```javascript
// util.mjs
export function greet(name) {
  return `안녕하세요, ${name}!`;
}
```

```javascript
// index.mjs
import { greet } from './util.mjs';

console.log(greet('영희')); // "안녕하세요, 영희!"
```

📌 **Node.js에서 ESM을 사용하려면?**

1. 파일 확장자를 `.mjs`로 지정하거나,
2. `package.json`에서 `"type": "module"` 설정을 추가해야 함

---

## 📌 요약

- **모듈을 사용하지 않으면 전역 범위(global scope) 오염과 함수 충돌 발생**
- **ES6 모듈(`import` / `export`)을 사용하여 코드 모듈화 가능**
- **`export`를 사용해 여러 개의 변수 및 함수 내보내기 가능**
- **`export default`를 사용하면 한 개의 기본 값을 내보낼 수 있음**
- **브라우저에서는 `<script type="module">`을 사용해야 함**
- **Node.js에서는 CommonJS(`require`) 또는 ESM(`import`) 방식 사용 가능**

JavaScript 모듈 시스템을 이해하면 코드의 유지보수성과 재사용성이 더욱 향상됩니다! 🚀
