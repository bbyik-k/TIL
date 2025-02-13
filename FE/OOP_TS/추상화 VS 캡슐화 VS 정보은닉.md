# **객체 지향 프로그래밍(OOP) 개념: 추상화 vs 캡슐화 vs 정보 은닉**

## **1️⃣ 추상화 (Abstraction)**

**📌 개념**

- **불필요한 세부 정보를 숨기고, 중요한 정보만 노출하는 개념**
- 복잡한 내부 구현을 감추고, 필요한 기능만 제공
- **인터페이스(interface) 또는 추상 클래스(abstract class)로 구현**

**✅ 예제 (TypeScript)**

```ts
// 추상 클래스
abstract class Animal {
  constructor(public name: string) {}

  abstract makeSound(): void; // 구체적인 구현 없음 (추상화)
}

class Dog extends Animal {
  makeSound(): void {
    console.log('멍멍!');
  }
}

const dog = new Dog('바둑이');
dog.makeSound(); // "멍멍!"
```

✅ **`Animal` 클래스는 `makeSound()` 메서드를 정의하지만 구현하지 않음.**
✅ **상속받은 `Dog` 클래스에서 구체적인 구현을 제공.**

---

## **2️⃣ 캡슐화 (Encapsulation)**

**📌 개념**

- **객체의 속성과 메서드를 하나로 묶고, 외부에서 직접 접근을 제한하는 개념**
- **데이터 보호 및 코드 유지보수성을 향상**
- `private`, `protected`, `public` 접근 제한자를 사용

**✅ 예제 (TypeScript)**

```ts
class BankAccount {
  private balance: number = 0; // 외부에서 직접 접근 불가

  deposit(amount: number): void {
    if (amount > 0) {
      this.balance += amount;
      console.log(`💰 ${amount}원이 입금됨. 현재 잔액: ${this.balance}원`);
    }
  }

  getBalance(): number {
    return this.balance; // balance에 직접 접근하지 못하고 메서드를 통해서만 확인 가능
  }
}

const account = new BankAccount();
account.deposit(1000);
console.log(account.getBalance()); // 1000
// console.log(account.balance); // ❌ 오류 발생 (private 변수 접근 불가)
```

✅ **`balance` 변수는 `private`이므로 직접 접근할 수 없음.**
✅ **메서드(`deposit`, `getBalance`)를 통해서만 데이터를 변경 가능.**

---

## **3️⃣ 정보 은닉 (Information Hiding)**

**📌 개념**

- **객체 내부의 데이터와 동작을 외부에서 직접 수정할 수 없도록 숨기는 개념**
- 캡슐화의 한 부분으로, **불필요한 접근을 차단하여 보안과 안정성을 향상**
- `private`, `protected`를 활용하여 데이터 보호

**✅ 예제 (TypeScript)**

```ts
class User {
  private password: string; // 외부에서 접근 불가

  constructor(password: string) {
    this.password = this.encryptPassword(password);
  }

  private encryptPassword(password: string): string {
    return password.split('').reverse().join(''); // 간단한 암호화 예제
  }

  getPassword(): string {
    return '비밀번호는 확인할 수 없습니다.';
  }
}

const user = new User('mySecret');
console.log(user.getPassword()); // "비밀번호는 확인할 수 없습니다."
// console.log(user.password); // ❌ 오류 발생 (private 속성 접근 불가)
```

✅ **비밀번호를 직접 접근하지 못하게 `private` 처리.**
✅ **외부에서는 암호화된 데이터를 볼 수 없고, 안전한 방식으로만 관리 가능.**

---

## **🚀 개념 비교 정리**

| 개념                               | 정의                                         | 목적                              | 사용 예시                         |
| ---------------------------------- | -------------------------------------------- | --------------------------------- | --------------------------------- |
| **추상화 (Abstraction)**           | 불필요한 세부 정보 숨기고 중요한 기능만 제공 | 코드의 복잡성 감소, 유지보수 용이 | 인터페이스, 추상 클래스 사용      |
| **캡슐화 (Encapsulation)**         | 속성과 메서드를 하나로 묶고, 접근 제한       | 데이터 보호, 코드 안정성 증가     | `private`, `protected` 사용       |
| **정보 은닉 (Information Hiding)** | 내부 데이터를 숨기고 안전하게 관리           | 보안 강화, 데이터 무결성 유지     | `private` 변수, 게터(getter) 사용 |

✅ **추상화는 불필요한 세부 정보를 감추고, 핵심 기능만 제공하는 것.**  
✅ **캡슐화는 데이터와 메서드를 묶고, 불필요한 접근을 제한하는 것.**  
✅ **정보 은닉은 중요한 데이터가 직접 수정되지 않도록 보호하는 것.**

📌 **즉, 캡슐화가 정보 은닉을 포함하는 더 큰 개념이며, 추상화는 외부에서 객체를 쉽게 사용할 수 있도록 하는 개념!** 🚀
