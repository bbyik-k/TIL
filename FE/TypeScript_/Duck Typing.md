# TypeScript의 Duck Typing (덕 타이핑)

## 1. 덕 타이핑(Duck Typing)이란?

덕 타이핑(Duck Typing)은 **객체의 변수 및 메소드의 집합이 객체의 타입을 결정하는 개념**입니다. 이는 컴파일 타임에 타입이 결정되는 정적 타입 시스템과 대비되는 개념으로, **객체가 특정 동작을 수행할 수 있다면 해당 객체를 특정 타입으로 간주할 수 있습니다.**

> "만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥거리는 소리를 낸다면 나는 그 새를 오리라고 부를 것이다."

덕 타이핑은 **클래스 상속이나 인터페이스 구현 여부에 관계없이** 객체가 특정 속성과 메서드를 가지고 있다면 해당 타입에 속하는 것으로 간주합니다.

---

## 2. TypeScript에서의 덕 타이핑

TypeScript는 정적 타입 언어이지만, **구조적 타입 시스템(Structural Typing)을 지원하며, 이는 덕 타이핑과 유사한 개념**입니다.

### **(1) 덕 타이핑 예제**

```ts
interface Bird {
  fly(): void;
  sing(): void;
}

class Sparrow {
  fly() {
    console.log('The sparrow is flying!');
  }
  sing() {
    console.log('Chirp chirp!');
  }
}

function makeBirdFly(bird: Bird) {
  bird.fly();
}

const sparrow = new Sparrow();
makeBirdFly(sparrow); // ✅ 정상 동작
```

✔ `Sparrow` 클래스는 `Bird` 인터페이스를 **명시적으로 구현하지 않았지만**, `fly()`와 `sing()` 메서드를 가지고 있기 때문에 **덕 타이핑에 의해 `Bird` 타입으로 간주될 수 있습니다.**

---

### **(2) 인터페이스를 구현하지 않아도 타입이 맞으면 사용 가능**

```ts
interface Writer {
  write(): void;
}

class Journalist {
  write() {
    console.log('Writing an article...');
  }
}

class Programmer {
  write() {
    console.log('Writing code...');
  }
}

function startWriting(writer: Writer) {
  writer.write();
}

const journalist = new Journalist();
const programmer = new Programmer();

startWriting(journalist); // ✅ "Writing an article..."
startWriting(programmer); // ✅ "Writing code..."
```

✔ `Journalist`와 `Programmer` 클래스는 `Writer` 인터페이스를 명시적으로 구현하지 않았지만, `write()` 메서드를 가지고 있기 때문에 **덕 타이핑에 의해 `Writer` 타입으로 간주됩니다.**

---

## 3. 덕 타이핑과 구조적 타이핑(Structural Typing)의 관계

TypeScript는 명시적인 인터페이스 구현 없이도 객체의 구조가 특정 인터페이스와 일치하면 해당 인터페이스 타입으로 사용할 수 있습니다. 이를 **구조적 타이핑(Structural Typing)**이라고 하며, 덕 타이핑과 유사한 개념입니다.

```ts
interface Car {
  drive(): void;
}

class Tesla {
  drive() {
    console.log('Driving a Tesla');
  }
}

const myCar: Car = new Tesla(); // ✅ 구조적 타이핑으로 인해 정상 할당
myCar.drive(); // ✅ "Driving a Tesla"
```

✔ `Tesla` 클래스는 `Car` 인터페이스를 직접 구현하지 않았지만, **`drive()` 메서드를 가지고 있기 때문에 `Car` 타입으로 사용할 수 있습니다.**

---

## 4. 덕 타이핑의 장점과 단점

### **✅ 장점**

- **유연한 타입 검사**: 인터페이스를 명시적으로 구현하지 않아도 타입이 맞으면 사용할 수 있음.
- **코드 재사용성 증가**: 특정 인터페이스를 구현하지 않아도, 메서드만 맞으면 사용할 수 있어 재사용성이 높아짐.
- **객체 간의 결합도를 낮춤**: 강한 타입 체크 없이 객체의 동작만을 기준으로 타입을 결정.

### **❌ 단점**

- **예상치 못한 타입 오류 가능성**: 타입스크립트의 강력한 타입 시스템을 활용하기 어렵거나, 실수로 잘못된 타입을 사용할 가능성이 있음.
- **코드 이해도 저하**: 명시적으로 인터페이스를 구현하지 않으면, 어떤 타입을 따르는지 이해하기 어려울 수 있음.

---

## **🚀 결론**

- **TypeScript에서 덕 타이핑은 구조적 타이핑(Structural Typing)과 밀접한 관계가 있음**.
- **객체가 특정 메서드와 속성을 가지고 있다면, 해당 객체를 특정 타입으로 간주할 수 있음**.
- **덕 타이핑은 유연한 코드를 작성할 수 있게 하지만, 예상치 못한 타입 오류가 발생할 가능성이 있음**.

👉 **즉, TypeScript에서 덕 타이핑을 활용하면 강한 타입 시스템을 유지하면서도 유연한 프로그래밍이 가능!** 🚀
