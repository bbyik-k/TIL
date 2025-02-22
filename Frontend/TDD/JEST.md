# Jest: JavaScript 테스트 프레임워크

## Jest란?

Jest는 페이스북에서 개발한 JavaScript 테스트 프레임워크로, 설정이 간단하고 강력한 기능을 제공하는 것으로 유명합니다. Jest는 단위 테스트(Unit Test), 통합 테스트(Integration Test), 스냅샷 테스트(Snapshot Test) 등을 지원하며, React와 같은 프레임워크와도 잘 어울립니다.

## Jest의 특징

- **설정이 간단함**: 별다른 설정 없이 바로 사용 가능
- **빠른 실행 속도**: 병렬 실행을 통해 빠르게 테스트 수행
- **스냅샷 테스트 지원**: UI 변경 사항을 감지 가능
- **모의(Mock) 함수 지원**: 함수 호출을 쉽게 추적 가능
- **TypeScript 지원**: TypeScript 프로젝트에서도 원활히 동작

## Jest 설치하기

프로젝트에서 Jest를 사용하려면 아래 명령어로 설치할 수 있습니다.

```sh
npm install --save-dev jest
```

또는 Yarn을 사용할 경우:

```sh
yarn add --dev jest
```

## Jest 기본 사용법

### 1. 테스트 파일 생성

Jest는 `__tests__` 폴더 내의 파일이나 `.test.js`, `.spec.js` 확장자를 가진 파일을 자동으로 인식합니다.

예제 파일: `sum.test.js`

```js
const sum = (a, b) => a + b;

test('1 + 2는 3이어야 한다.', () => {
  expect(sum(1, 2)).toBe(3);
});
```

### 2. 테스트 실행

아래 명령어로 테스트를 실행할 수 있습니다.

```sh
npx jest
```

또는 Yarn을 사용할 경우:

```sh
yarn jest
```

## Jest의 주요 기능

### 1. **Matcher 사용하기**

Jest는 다양한 Matcher를 제공하여 테스트 결과를 검증할 수 있습니다.

```js
test('객체 비교', () => {
  const data = { one: 1 };
  data.two = 2;
  expect(data).toEqual({ one: 1, two: 2 });
});
```

### 2. **비동기 코드 테스트**

Jest는 `async/await`, `Promises`를 사용한 비동기 코드도 테스트할 수 있습니다.

```js
const fetchData = () => Promise.resolve('Hello Jest');

test('비동기 코드 테스트', async () => {
  const data = await fetchData();
  expect(data).toBe('Hello Jest');
});
```

### 3. **Mock 함수 활용**

모의(Mock) 함수를 사용하여 특정 함수의 동작을 대체할 수 있습니다.

```js
test('Mock 함수 사용', () => {
  const mockFn = jest.fn(() => 'Mocked');
  expect(mockFn()).toBe('Mocked');
  expect(mockFn).toHaveBeenCalled();
});
```

## 마무리

Jest는 JavaScript 애플리케이션의 테스트를 간편하게 수행할 수 있는 강력한 도구입니다. 앞으로 Jest의 심화 기능과 다양한 활용법을 탐구해보겠습니다.

---

## 앞으로 더 알아볼 주제

1. Jest의 **Mocking 기법** 심층 분석
2. Jest로 **React 컴포넌트 테스트**하기
3. Jest와 **React Testing Library** 활용법
4. Jest에서 **스냅샷 테스트** 활용법
5. Jest의 **비동기 테스트** 깊이 이해하기
6. Jest에서 **커버리지 리포트(coverage report) 분석**
7. Jest 설정 최적화 및 CI/CD 환경에서 활용법
