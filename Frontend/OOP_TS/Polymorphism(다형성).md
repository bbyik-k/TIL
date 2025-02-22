# 📌 TypeScript에서의 다형성

## 🔹 다형성이란?

다형성(Polymorphism)은 객체 지향 프로그래밍(OOP)의 핵심 원칙 중 하나로, 부모 클래스의 객체를 자식 클래스의 객체처럼 다룰 수 있도록 하는 개념입니다. 이를 통해 코드의 유연성과 재사용성을 높일 수 있습니다.

## 🔹 다형성의 종류

1. **메서드 오버라이딩** (런타임 다형성)
2. **메서드 오버로딩** (컴파일 타임 다형성)
3. **인터페이스 기반 다형성**

---

## 🔹 1. 메서드 오버라이딩 (런타임 다형성)

자식 클래스가 부모 클래스의 메서드를 자신에게 맞게 재정의하는 방식입니다.

```typescript
class Animal {
  makeSound(): void {
    console.log('일반적인 동물 소리');
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log('멍멍!');
  }
}

class Cat extends Animal {
  makeSound(): void {
    console.log('야옹!');
  }
}

let animals: Animal[] = [new Dog(), new Cat()];
animals.forEach((animal) => animal.makeSound());
```

📌 **핵심 포인트:** `makeSound()` 메서드는 객체의 실제 타입에 따라 다르게 동작합니다.

---

## 🔹 2. 메서드 오버로딩 (컴파일 타임 다형성)

TypeScript에서는 여러 개의 함수 시그니처를 정의하여 오버로딩을 구현할 수 있습니다.

```typescript
class MathOperations {
  add(a: number, b: number): number;
  add(a: string, b: string): string;
  add(a: any, b: any): any {
    return a + b;
  }
}

const math = new MathOperations();
console.log(math.add(5, 10)); // 15
console.log(math.add('안녕', ' 세계')); // "안녕 세계"
```

📌 **핵심 포인트:** TypeScript에서는 여러 개의 함수 시그니처를 정의하고 하나의 구현을 제공해야 합니다.

---

## 🔹 3. 인터페이스 기반 다형성

여러 클래스가 공통 인터페이스를 구현하도록 함으로써 일관된 구조를 유지할 수 있습니다.

```typescript
interface Vehicle {
  drive(): void;
}

class Car implements Vehicle {
  drive(): void {
    console.log('자동차를 운전 중');
  }
}

class Bike implements Vehicle {
  drive(): void {
    console.log('자전거를 타는 중');
  }
}

let vehicles: Vehicle[] = [new Car(), new Bike()];
vehicles.forEach((vehicle) => vehicle.drive());
```

📌 **핵심 포인트:** 인터페이스를 사용하면 여러 개의 클래스를 동일한 방식으로 다룰 수 있습니다.

---

## 🔹 다형성의 장점

✅ 코드 재사용성과 유지보수성 향상
✅ 중복 코드 감소
✅ 확장성과 유연성 증가
✅ 동적 메서드 바인딩 가능

---

## 📌 요약

TypeScript에서 다형성을 활용하면 다양한 타입의 객체를 일관된 방식으로 다룰 수 있습니다. 주요 개념은 다음과 같습니다:

- **메서드 오버라이딩**: 런타임 시 다른 동작 수행
- **메서드 오버로딩**: 컴파일 타임 시 여러 함수 시그니처 정의
- **인터페이스 기반 다형성**: 공통 구조를 정의하여 다양한 클래스에서 활용 가능

다형성을 잘 활용하면 TypeScript 애플리케이션의 품질과 확장성이 크게 향상될 수 있습니다.
