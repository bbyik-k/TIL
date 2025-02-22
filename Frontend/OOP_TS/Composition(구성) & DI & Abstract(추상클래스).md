# Composition (구성) & DI (Dependency Injection) & Abstract class (추상클래스)

## 1. Composition (구성)

### **✔ Composition이란?**

Composition(구성)이란 객체 지향 프로그래밍(OOP)에서 **클래스를 조합하여 기능을 확장하는 방법**을 의미합니다.

### **✔ Composition vs. 상속 (Inheritance)**

- **상속(Inheritance)**: 부모 클래스를 기반으로 자식 클래스가 기능을 상속받음.
- **구성(Composition)**: 클래스가 다른 클래스를 포함하여 기능을 확장하는 방식.

> **💡 상속 대신 Composition을 더 선호하는 이유**
>
> - **디커플링(Decoupling)**: 강한 결합을 피하고, 독립적인 코드 재사용이 가능.
> - **유연성 증가**: 여러 개의 작은 클래스를 조합하여 새로운 기능을 만들기 쉬움.
> - **다중 상속 문제 해결**: 자바스크립트/타입스크립트는 다중 상속을 지원하지 않으므로, Composition을 통해 여러 기능을 조합 가능.

### **✔ Composition 예제 (객체를 직접 포함)**

```ts
class Engine {
  start() {
    console.log('Engine starting...');
  }
}

class Wheels {
  roll() {
    console.log('Wheels rolling...');
  }
}

class Car {
  private engine = new Engine();
  private wheels = new Wheels();

  drive() {
    this.engine.start();
    this.wheels.roll();
    console.log('Car is moving...');
  }
}

const myCar = new Car();
myCar.drive();
```

✔ **엔진(Engine)과 바퀴(Wheels)를 \*\***`Car`\***\* 내부에서 직접 생성하고 사용 → Composition 방식!**
✔ **이 방식은 간단하지만, \*\***`Car`\***\*가 \*\***`Engine`\***\*과 \*\***`Wheels`\***\*에 강하게 결합되어 있어 변경이 어려울 수 있음.**

---

## 2. Dependency Injection (의존성 주입, DI)

### **✔ DI란?**

Dependency Injection(DI, 의존성 주입)은 **객체의 생성과 의존성 관리를 외부에서 처리하는 개념**입니다.

> **💡 왜 DI를 사용할까?**
>
> - **코드의 유연성과 재사용성 증가**: 의존성의 변경이 용이함.
> - **테스트 용이성**: Mock 객체를 주입하여 단위 테스트가 쉬워짐.
> - **객체 간 결합도 감소**: 객체가 직접 인스턴스를 생성하는 것이 아니라, 외부에서 제공받음.

### **✔ DI를 활용한 Composition 예제**

```ts
class Engine {
  start() {
    console.log('Engine starting...');
  }
}

class Wheels {
  roll() {
    console.log('Wheels rolling...');
  }
}

class Car {
  constructor(private engine: Engine, private wheels: Wheels) {}

  drive() {
    this.engine.start();
    this.wheels.roll();
    console.log('Car is moving...');
  }
}

// DI를 이용한 객체 주입
const engine = new Engine();
const wheels = new Wheels();
const myCar = new Car(engine, wheels);
myCar.drive();
```

✔ **DI 방식에서는 \*\***`Car`\***\*가 \*\***`Engine`\***\*과 \*\***`Wheels`\***\*를 직접 생성하지 않고, 외부에서 주입받음.**
✔ **이 방식은 결합도를 낮춰 테스트 및 유지보수를 용이하게 함.**
✔ **Mock 객체나 다양한 엔진 유형을 쉽게 교체할 수 있음.**

---

## 3. Abstract (추상화 & 추상 클래스)

### **✔ 추상화(Abstract)란?**

추상화란 **객체의 핵심적인 부분만 노출하고, 불필요한 구현 세부 사항을 숨기는 개념**입니다.

> **💡 왜 추상화를 사용할까?**
>
> - **코드의 명확성 증가**: 인터페이스 또는 추상 클래스를 사용하여 설계를 명확하게 정의할 수 있음.
> - **코드의 유지보수성 증가**: 특정 기능을 캡슐화하여 코드 변경 시 영향을 최소화할 수 있음.
> - **일관된 인터페이스 제공**: 여러 개의 클래스가 동일한 인터페이스를 따를 수 있음.

### **✔ 추상 클래스(abstract class)란?**

- **추상 클래스는 인스턴스화할 수 없는 클래스**입니다.
- **공통된 기능을 정의하고, 하위 클래스에서 반드시 구현해야 할 메서드를 강제**할 수 있습니다.

### **✔ 추상 클래스 예제**

```ts
abstract class Animal {
  constructor(protected name: string) {}

  abstract makeSound(): void; // 반드시 하위 클래스에서 구현해야 함

  move(): void {
    console.log(`${this.name} is moving.`);
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log('Woof! Woof!');
  }
}

class Cat extends Animal {
  makeSound(): void {
    console.log('Meow! Meow!');
  }
}

const myDog = new Dog('Buddy');
myDog.makeSound(); // ✅ "Woof! Woof!"
myDog.move(); // ✅ "Buddy is moving."
```

✔ **`Animal`\*\***은 \***\*`abstract class`\*\***로, \***\*`makeSound()`\*\***는 하위 클래스에서 반드시 구현해야 함.**
✔ **`Dog`\***\*, \*\***`Cat`\***\*은 \*\***`Animal`\***\*을 상속받아 각자의 \*\***`makeSound()`\***\*를 구현.**

---

## **🚀 결론**

### **💡 Composition, DI, 추상 클래스의 차이점 정리**

| 개념                             | 설명                                                                          | 사용 예제                                              |
| -------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------ |
| **Composition (구성)**           | 여러 개의 독립적인 클래스를 조합하여 기능을 확장하지만, 의존성이 높을 수 있음 | `Car` 클래스가 내부에서 `Engine`, `Wheels`를 직접 생성 |
| **Dependency Injection (DI)**    | 외부에서 객체를 주입하여 결합도를 낮춤                                        | `Car` 클래스가 외부에서 `Engine`, `Wheels`를 주입받음  |
| **추상 클래스 (Abstract Class)** | 공통된 기능을 정의하고, 특정 메서드 구현을 강제                               | `Animal` 클래스가 `makeSound()`를 강제                 |

👉 **즉, 상속보다는 Composition을 선호하며, 필요할 경우 DI와 추상화를 통해 유지보수성과 확장성을 높일 수 있습니다!** 🚀
