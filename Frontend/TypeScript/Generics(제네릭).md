# TypeScript의 Generics (제네릭)

## 1. Generics (제네릭)이란?

제네릭(Generics)은 **타입을 변수처럼 사용할 수 있는 기능**으로, **코드의 재사용성을 높이고, 타입 안정성을 유지하면서도 유연한 코드를 작성할 수 있도록 도와주는 강력한 기능**입니다.

> **💡 제네릭을 사용하는 이유**
>
> - **코드의 재사용성 증가**: 같은 코드 구조에서 다양한 타입을 사용할 수 있음.
> - **타입 안정성 보장**: 컴파일 단계에서 타입을 체크할 수 있음.
> - **유연한 타입 적용**: 특정 타입을 제한하거나, 다형성을 활용할 수 있음.

---

## 2. 기본적인 제네릭 사용법

### **✔ 제네릭 함수**

```ts
function identity<T>(arg: T): T {
  return arg;
}

const numberResult = identity<number>(10); // ✅ number 타입 지정
const stringResult = identity('Hello'); // ✅ 타입 추론 가능
```

✔ **`T`는 함수 호출 시점에서 타입을 결정하며, 함수 내부에서 사용됨.**
✔ **명시적으로 `<number>`를 지정할 수도 있지만, 타입 추론이 가능하므로 생략 가능.**

---

### **✔ 제네릭 인터페이스**

```ts
interface Box<T> {
  value: T;
}

const numberBox: Box<number> = { value: 42 };
const stringBox: Box<string> = { value: 'Hello' };
```

✔ **인터페이스의 타입을 유동적으로 설정할 수 있어, 여러 타입을 지원하는 유연한 구조를 만들 수 있음.**

---

### **✔ 제네릭 클래스**

```ts
class DataStorage<T> {
  private data: T[] = [];

  addItem(item: T) {
    this.data.push(item);
  }

  removeItem(item: T) {
    this.data = this.data.filter((i) => i !== item);
  }

  getItems(): T[] {
    return [...this.data];
  }
}

const textStorage = new DataStorage<string>();
textStorage.addItem('Hello');
textStorage.addItem('World');
textStorage.removeItem('Hello');
console.log(textStorage.getItems()); // ✅ ["World"]
```

✔ **제네릭을 활용하여 다양한 타입을 지원하는 유연한 클래스를 생성할 수 있음.**
✔ **배열을 기반으로 데이터를 저장하는 `DataStorage` 클래스를 만들었으며, `T`를 사용해 타입을 유동적으로 관리 가능.**

---

## 3. 제네릭의 심화 개념

### **✔ 제네릭 타입 제한 (Constraints)**

```ts
function merge<T extends object, U extends object>(objA: T, objB: U) {
  return { ...objA, ...objB };
}

const merged = merge({ name: 'John' }, { age: 30 });
console.log(merged); // ✅ { name: "John", age: 30 }
```

✔ **`T extends object`, `U extends object`를 사용하여 `T`와 `U`가 객체 타입임을 보장.**
✔ **제네릭을 사용할 때 특정 타입을 제한할 수 있어 안전성을 높일 수 있음.**

---

### **✔ keyof 키워드와 제네릭 사용**

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const person = { name: 'Alice', age: 25 };
console.log(getProperty(person, 'name')); // ✅ "Alice"
```

✔ **`keyof T`를 사용하면 객체의 키 중 하나만 받을 수 있도록 제한 가능.**
✔ **제네릭을 활용하여 객체의 특정 속성을 안전하게 다룰 수 있음.**

---

### **✔ 제네릭 인터페이스 & 함수 조합**

```ts
interface Lengthy {
  length: number;
}

function logLength<T extends Lengthy>(item: T): T {
  console.log(item.length);
  return item;
}

logLength('Hello'); // ✅ 5
logLength([1, 2, 3, 4]); // ✅ 4
```

✔ **인터페이스 `Lengthy`를 활용하여 `length` 속성이 있는 타입만 허용하도록 제한.**
✔ **문자열, 배열 등 `length` 속성이 있는 객체는 모두 사용 가능.**

---

## 4. 제네릭을 더 잘 활용하는 방법 (심화 학습)

### **✔ 다양한 제네릭 사용법**

| 패턴                          | 예제                            |
| ----------------------------- | ------------------------------- |
| **제네릭 다중 타입**          | `<T, U>(a: T, b: U): [T, U]`    |
| **제네릭 기본값 설정**        | `<T = string>() => T`           |
| **제네릭과 인터페이스 조합**  | `interface Box<T> { value: T }` |
| **제네릭 유틸리티 타입 사용** | `Partial<T>`, `Readonly<T>`     |

---

## 5. 🚀 제네릭 학습 추가 공부 TODO

### **💡 추천 학습 주제**

- **제네릭 타입 유틸리티 (`Partial<T>`, `Readonly<T>`, `Record<K, T>` 등) 활용**
- **제네릭과 인터페이스 및 타입 별칭(Type Alias) 활용 방법**
- **제네릭을 활용한 고급 패턴 (커스텀 훅, 상태 관리 등)**
- **TypeScript에서 제네릭을 활용한 라이브러리 코드 분석 (예: `React.FC<T>`)**

### **💡 추천 실습 과제**

1. **제네릭을 활용한 유틸리티 함수 작성하기**
2. **제네릭을 사용하여 CRUD 가능한 데이터 관리 클래스 만들기**
3. **제네릭을 사용하여 다양한 타입의 데이터 필터링 함수 구현하기**
4. **제네릭과 keyof를 활용하여 객체의 속성을 안전하게 접근하는 유틸리티 함수 작성**

---

## **🚀 결론**

- **제네릭은 TypeScript에서 코드의 재사용성과 타입 안정성을 극대화하는 핵심 기능**
- **기본적인 제네릭 활용법을 익힌 후, 제네릭 타입 제한, 유틸리티 타입, 고급 패턴을 추가 학습하면 좋음**
- **TypeScript의 강력한 타입 시스템을 활용하여 유지보수성이 높은 코드 작성 가능**
