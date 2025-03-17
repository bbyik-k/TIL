# TypeScript의 Decorator (@)

## 1. Decorator란?

**Decorator**는 TypeScript에서 **클래스, 메서드, 프로퍼티, 액세서 등에 추가적인 기능을 부여**할 수 있는 특수한 문법입니다.

- `@`(at) 기호를 사용하여 정의됨
- **함수 형태**로 작성되며, 메타프로그래밍을 가능하게 함
- 주로 **클래스 기반의 프레임워크(NestJS, Angular 등)** 에서 활용됨

---

## 2. Decorator의 종류

TypeScript에서 사용 가능한 Decorator 유형은 다음과 같습니다:

### 2.1 클래스 데코레이터 (Class Decorator)

클래스 자체를 대상으로 하는 데코레이터입니다.

```ts
function MyDecorator(constructor: Function) {
  console.log('클래스가 생성됨: ', constructor.name);
}

@MyDecorator
class ExampleClass {
  constructor() {
    console.log('ExampleClass 인스턴스 생성');
  }
}
```

✅ **출력:**

```
클래스가 생성됨: ExampleClass
ExampleClass 인스턴스 생성
```

> 📌 **클래스가 정의될 때 실행**되며, 주로 로깅, 메타데이터 저장 등에 사용됩니다.

---

### 2.2 메서드 데코레이터 (Method Decorator)

메서드에 특정한 기능을 추가할 때 사용됩니다.

```ts
function LogMethod(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  descriptor.value = function (...args: any[]) {
    console.log(`메서드 호출됨: ${propertyKey}, 인자: ${JSON.stringify(args)}`);
    return originalMethod.apply(this, args);
  };
  return descriptor;
}

class Example {
  @LogMethod
  sayHello(name: string) {
    console.log(`Hello, ${name}!`);
  }
}

const e = new Example();
e.sayHello('TypeScript');
```

✅ **출력:**

```
메서드 호출됨: sayHello, 인자: ["TypeScript"]
Hello, TypeScript!
```

> 📌 **로깅, 성능 측정, 실행 횟수 제한 등의 기능을 추가하는 데 유용함**

**로깅 예제 2**

```ts
{
  function Log(_: any, name: string, descriptor: PropertyDescriptor): PropertyDescriptor {
    const newDescriptor = {
      ...descriptor,
      value: function (...args: any[]): any {
        console.log(`Calling ${name} with arguments:`);
        console.dir(args);
        const result = descriptor.value.apply(this, args);
        console.log(`Result:`);
        console.dir(result);
        return result;
      },
    };
    return newDescriptor;
  }

  class Calculator {
    @Log
    add(x: number, y: number): number {
      return x + y;
    }
  }

  const calculator = new Calculator();
  console.log(calculator.add(1, 2));
}
```

---

### 2.3 프로퍼티 데코레이터 (Property Decorator)

클래스의 속성(프로퍼티)에 특정한 동작을 추가할 때 사용됩니다.

```ts
function ReadOnly(target: any, propertyKey: string) {
  Object.defineProperty(target, propertyKey, {
    writable: false,
  });
}

class Example {
  @ReadOnly
  name: string = 'TypeScript';
}

const e = new Example();
e.name = 'JavaScript'; // Error: Cannot assign to read-only property
```

> 📌 **읽기 전용 속성을 만들거나, 기본값을 설정하는 데 유용함**

---

### 2.4 액세서 데코레이터 (Accessor Decorator)

Getter 또는 Setter에 적용되어 실행을 제어합니다.

```ts
function LogAccessor(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalGet = descriptor.get;
  descriptor.get = function () {
    console.log(`Getter 호출됨: ${propertyKey}`);
    return originalGet?.apply(this);
  };
  return descriptor;
}

class Example {
  private _name: string = 'TypeScript';

  @LogAccessor
  get name() {
    return this._name;
  }
}

const e = new Example();
console.log(e.name);
```

✅ **출력:**

```
Getter 호출됨: name
TypeScript
```

> 📌 **프로퍼티의 접근을 추적하거나, 캐싱 등의 기능을 추가하는 데 유용함**

---

### 2.5 매개변수 데코레이터 (Parameter Decorator)

메서드의 매개변수에 대한 정보를 제공할 때 사용됩니다.

```ts
function LogParameter(target: any, propertyKey: string, parameterIndex: number) {
  console.log(`메서드 ${propertyKey}의 ${parameterIndex}번째 인자에 데코레이터 적용됨`);
}

class Example {
  greet(@LogParameter name: string) {
    console.log(`Hello, ${name}!`);
  }
}
```

✅ **출력:**

```
메서드 greet의 0번째 인자에 데코레이터 적용됨
```

> 📌 **메서드의 인자를 검증하거나, API 요청 파라미터를 로깅하는 데 사용 가능**

---

## 3. 실무 적용 사례

TypeScript의 Decorator는 **프레임워크 및 라이브러리에서 핵심적으로 사용**됩니다.

### **✅ NestJS에서의 사용 (클래스 기반 API 서버)**

```ts
import { Injectable, Controller, Get } from '@nestjs/common';

@Injectable()
class ExampleService {
  getHello(): string {
    return 'Hello, NestJS!';
  }
}

@Controller()
class ExampleController {
  constructor(private readonly exampleService: ExampleService) {}

  @Get('hello')
  sayHello() {
    return this.exampleService.getHello();
  }
}
```

> 📌 `@Injectable`, `@Controller`, `@Get` 등은 NestJS에서 Decorator를 활용하여 **의존성 주입(DI)** 및 **라우팅 기능**을 구현합니다.

### **✅ Vue의 Composition API에서의 사용**

```ts
import { Options, Vue } from 'vue-class-component';

@Options({
  components: {},
})
export default class MyComponent extends Vue {
  message: string = 'Hello Vue!';
}
```

> 📌 Vue의 `@Options` 데코레이터를 활용하여 컴포넌트 설정을 관리할 수 있습니다.

---

## 4. Decorator의 장점

✅ **가독성 향상**: 로직을 깔끔하게 정리 가능  
✅ **코드 중복 감소**: 반복적인 기능을 함수로 재사용 가능  
✅ **프레임워크 활용**: NestJS, Angular 등에서 강력하게 지원  
✅ **메타프로그
래밍 가능**: 런타임에 동작을 동적으로 변경 가능

---

## 5. 결론

Decorator는 **TypeScript의 메타프로그래밍을 위한 강력한 도구**로, 프레임워크에서 널리 사용됩니다.  
실무에서는 **클래스 기반의 개발을 할 때 코드의 유지보수성과 가독성을 높이는 데** 유용합니다.  
NestJS, Angular 등에서 Decorator를 활용하여 **의존성 주입(DI), 라우팅, 로깅** 등의 기능을 효율적으로 구현할 수 있습니다.

## 참고자료

- [TypeScript Docs](https://www.typescriptlang.org/ko/docs/handbook/decorators.html#%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8C%A9%ED%86%A0%EB%A6%AC-decorator-factories)
- [[toss] NestJS 환경에 맞는 Custom Decorator 만들기
  ](https://toss.tech/article/nestjs-custom-decorator)
