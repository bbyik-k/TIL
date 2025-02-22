# 📌 JavaScript의 Prototype 개념

## 🔹 Prototype이란?

JavaScript는 프로토타입 기반(prototype-based) 언어로, 객체가 다른 객체를 상속받는 방식으로 동작합니다. JavaScript의 모든 객체는 `prototype`이라는 속성을 가지며, 이를 통해 상속이 이루어집니다.

## 🔹 Prototype Chain (프로토타입 체인)

객체에서 특정 속성이나 메서드를 찾을 때, 해당 객체에서 먼저 검색하고 없으면 그 객체의 프로토타입을 따라 올라가며 찾는 방식입니다.

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`안녕하세요, 저는 ${this.name}입니다.`);
};

const user = new Person('철수');
user.sayHello(); // 안녕하세요, 저는 철수입니다.
```

📌 **핵심 개념:** `user` 객체에는 `sayHello` 메서드가 직접 정의되지 않았지만, `Person.prototype`에서 찾아 실행됩니다.

## 🔹 ES6 이후의 Class와 Prototype

ES6에서 `class` 키워드가 도입되었지만, 이는 **문법적 설탕(Syntactic Sugar)**일 뿐, 내부적으로 여전히 프로토타입을 기반으로 동작합니다.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name}가 소리를 냅니다.`);
  }
}

const dog = new Animal('강아지');
dog.speak(); // 강아지가 소리를 냅니다.
```

📌 **핵심 개념:** `class`는 `prototype`을 기반으로 동작하며, `Animal.prototype.speak`로 정의됩니다.

---

# 📌 JavaScript의 `this`

## 🔹 `this`란?

JavaScript에서 `this`는 **함수를 호출하는 방식**에 따라 달라지는 특별한 키워드입니다.

## 🔹 `this`의 동작 방식

1. **전역 실행 시 (`window` or `globalThis`)**
   ```javascript
   console.log(this); // 브라우저에서는 window, Node.js에서는 globalThis
   ```
2. **객체 메서드에서의 `this`**
   ```javascript
   const obj = {
     name: '철수',
     greet() {
       console.log(this.name);
     },
   };
   obj.greet(); // "철수"
   ```
3. **생성자 함수에서의 `this`**
   ```javascript
   function Person(name) {
     this.name = name;
   }
   const me = new Person('영희');
   console.log(me.name); // "영희"
   ```
4. **이벤트 핸들러에서의 `this`**
   ```javascript
   document.querySelector('button').addEventListener('click', function () {
     console.log(this); // 클릭된 버튼 요소
   });
   ```
5. **`bind`, `call`, `apply`로 `this` 변경**

   ```javascript
   function sayHello() {
     console.log(this.name);
   }

   const user = { name: '철수' };
   sayHello.call(user); // "철수"
   sayHello.apply(user); // "철수"

   const boundFunc = sayHello.bind(user);
   boundFunc(); // "철수"
   ```

   📌 **추가 설명:**

   - `call(user)`: `this`를 `user`로 설정하고 즉시 실행.
   - `apply(user, [])`: `call`과 동일하지만, 두 번째 인자로 전달할 값을 배열 형태로 받음.
   - `bind(user)`: `this`가 `user`로 고정된 새로운 함수를 반환함.

   ```javascript
   function introduce(age, city) {
     console.log(`안녕하세요, 저는 ${this.name}입니다. 나이는 ${age}살이고, ${city}에 살고 있어요.`);
   }

   const person = { name: '영희' };
   introduce.call(person, 25, '서울'); // "안녕하세요, 저는 영희입니다. 나이는 25살이고, 서울에 살고 있어요."
   introduce.apply(person, [25, '서울']); // "안녕하세요, 저는 영희입니다. 나이는 25살이고, 서울에 살고 있어요."
   const boundIntroduce = introduce.bind(person, 25, '서울');
   boundIntroduce(); // "안녕하세요, 저는 영희입니다. 나이는 25살이고, 서울에 살고 있어요."
   ```

6. **화살표 함수에서의 `this`**
   ```javascript
   const obj = {
     name: '철수',
     greet: () => {
       console.log(this.name);
     },
   };
   obj.greet(); // undefined (화살표 함수는 `this`를 바인딩하지 않음)
   ```
   📌 **핵심 개념:** 화살표 함수는 자신만의 `this`를 가지지 않으며, 부모 스코프의 `this`를 그대로 사용합니다.

---

## 📌 요약

- **Prototype**: JavaScript는 프로토타입 기반의 상속 구조를 사용하며, ES6의 `class`도 내부적으로 `prototype`을 활용함.
- **Prototype Chain**: 객체가 속성/메서드를 찾을 때 자신의 프로토타입을 따라가며 검색함.
- **this**: 실행 문맥에 따라 `this`의 값이 달라지며, `bind`, `call`, `apply`를 통해 명시적으로 변경 가능함.
- **`call`, `apply`, `bind` 차이점:**
  - `call`과 `apply`는 즉시 실행
  - `apply`는 인자를 배열로 전달
  - `bind`는 새로운 함수를 반환하여 나중에 실행 가능
- **화살표 함수의 `this`**: 부모 스코프의 `this`를 따르므로 일반 함수와 다르게 동작함.

JavaScript의 `prototype`과 `this`를 이해하면 객체 지향 프로그래밍과 함수 실행 컨텍스트를 더 깊이 있게 다룰 수 있습니다. 🎯
