## 1. Type Aliases (타입 별칭)

**👉 역할:**

- 새로운 타입 이름을 정의하는 기능.
- 복잡한 타입을 가독성 있게 만들고 재사용할 수 있음.

### 📌 예제

```ts
type UserID = string | number; // 타입 별칭 정의

let id1: UserID = 123;
let id2: UserID = 'abc';
```

✅ `UserID`를 재사용 가능 → `string | number`를 반복해서 쓰지 않아도 됨.

---

## 2. Union Types: `|` (OR 연산자, 조합)

**👉 역할:**

- 여러 타입 중 하나를 허용하는 타입.
- `|`(파이프) 연산자를 사용하여 선언.

### 📌 예제

```ts
type Status = 'success' | 'error' | 'loading';

let currentStatus: Status = 'success'; // ✅ 가능
currentStatus = 'error'; // ✅ 가능
currentStatus = 'pending'; // ❌ 오류 (정의되지 않은 값)
```

✅ "success", "error", "loading" 중 하나만 가능.  
❌ "pending" 같은 정의되지 않은 값은 허용되지 않음.

---

## 3. Discriminated Union (판별자, 태그된 유니온)

**👉 역할:**

- 각 타입에 공통된 `discriminator` 속성을 추가하여 구분하는 Union Type.
- TypeScript가 타입을 추론할 때 도움을 줌.

### 📌 예제

```ts
type Circle = {
  kind: 'circle'; // 태그 (discriminator)
  radius: number;
};

type Rectangle = {
  kind: 'rectangle'; // 태그 (discriminator)
  width: number;
  height: number;
};

type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
  if (shape.kind === 'circle') {
    return Math.PI * shape.radius ** 2;
  } else {
    return shape.width * shape.height;
  }
}
```

✅ `kind` 속성을 사용해 타입을 구분할 수 있음.  
✅ 타입 안전성을 보장하며, `switch-case` 문에서 `never` 타입을 활용할 수 있음.

---

## 4. Intersection Types: `&` (교차로, AND 연산자)

**👉 역할:**

- 여러 타입을 조합하여 하나의 타입으로 만듦.
- `&` 연산자를 사용하여 정의.

### 📌 예제

```ts
type Person = { name: string };
type Employee = { company: string };

type EmployeeInfo = Person & Employee; // 두 타입을 결합

const emp: EmployeeInfo = {
  name: 'Alice',
  company: 'OpenAI',
};
```

✅ `EmployeeInfo`는 `Person`과 `Employee`의 속성을 모두 가져야 함.  
✅ 객체 속성을 확장할 때 유용함.

---

## 5. `enum` (열거형, enumerated type)

**👉 역할:**

- 여러 개의 관련된 값을 하나의 그룹으로 묶는 기능.
- 숫자 또는 문자열 값을 가짐.

### 📌 숫자형 `enum`

```ts
enum Direction {
  Up, // 0
  Down, // 1
  Left, // 2
  Right, // 3
}

const move = Direction.Up; // 0
```

### 📌 문자열형 `enum`

```ts
enum Status {
  Success = 'SUCCESS',
  Error = 'ERROR',
  Loading = 'LOADING',
}

const currentStatus: Status = Status.Success;
console.log(currentStatus); // "SUCCESS"
```

✅ 연관된 상수를 그룹화할 때 유용함.  
✅ 숫자 또는 문자열 값을 자동으로 할당함.

---

## 6. Type Inference (타입 추론)

**👉 역할:**

- TypeScript가 자동으로 변수의 타입을 추론하는 기능.
- `: 타입`을 명시하지 않아도 TypeScript가 타입을 결정.

### 📌 예제

```ts
let message = 'Hello'; // TypeScript가 message를 'string'으로 추론
message = 123; // ❌ 오류 (string으로 추론되었기 때문)
```

✅ 명확한 타입 선언 없이도, TypeScript가 자동으로 타입을 지정.  
✅ 타입을 명시하지 않아도, 타입 안정성을 유지할 수 있음.

---

## 7. Type Assertions (타입 단언, 주장)

**👉 역할:**

- 개발자가 특정 타입임을 보장할 때 사용.
- TypeScript가 추론한 타입을 다른 타입으로 변환.

### 📌 `as` 키워드 사용

```ts
let value: any = 'hello';
let strLength: number = (value as string).length;

console.log(strLength); // 5
```

### 📌 `<Type>` 문법 사용

```ts
let value: any = 'hello';
let strLength: number = (<string>value).length;
```

✅ TypeScript가 잘못 추론한 경우 수동으로 타입을 설정할 수 있음.  
❌ 하지만 `null` 또는 `undefined`에 대한 검사는 직접 해야 함.

---

## 🚀 결론 요약

| 개념                         | 설명                        | 예제                                  |
| ---------------------------- | --------------------------- | ------------------------------------- | ------------------------ | --------- |
| **Type Aliases**             | 타입에 별칭을 지정          | `type ID = string                     | number;`                 |
| \*\*Union Types (`           | `)\*\*                      | 여러 타입 중 하나                     | `type Status = "success" | "error";` |
| **Discriminated Union**      | `kind` 속성으로 타입 구분   | `type Shape = Circle                  | Rectangle;`              |
| **Intersection Types (`&`)** | 여러 타입을 결합            | `type Employee = Person & Company;`   |
| **enum**                     | 여러 값을 그룹화            | `enum Status { Success, Error }`      |
| **Type Inference**           | TypeScript가 타입 자동 추론 | `let name = "Alice";`                 |
| **Type Assertions**          | 수동으로 타입 설정          | `let len = (value as string).length;` |

✅ TypeScript를 사용할 때 상황에 맞게 위 개념들을 활용하면 코드의 안정성과 가독성을 높일 수 있습니다! 🚀
