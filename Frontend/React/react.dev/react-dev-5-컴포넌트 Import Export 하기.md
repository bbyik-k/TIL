# React.dev-docs-5-UI 표현하기-컴포넌트 import 및 export 하기

[REACT 학습하기 > UI 표현하기 > 컴포넌트 import 및 export 하기](https://ko.react.dev/learn/importing-and-exporting-components)

## Goal

- Root 컴포넌트가 무엇인지 이해한다.
- 컴포넌트를 **import**하거나 **export**하는 기본 방법을 익힌다.
- **default**와 **named** 방식의 차이를 파악하고 사용 시점을 이해한다.
- 한 파일에서 여러 컴포넌트를 export하거나 import할 수 있는 상황을 숙지한다.
- 복잡한 구조에서는 컴포넌트를 여러 파일로 분리하는 이유와 방법을 알아둔다.

## Root 컴포넌트란

- 루트 컴포넌트는 애플리케이션의 진입점이 되는 파일 단위의 컴포넌트이다.
- 예시에서는 `App.js`라는 파일이 루트 컴포넌트를 정의하는 곳이며, 이 안에 `Gallery`와 `Profile` 컴포넌트를 포함한다.
- 프레임워크(예: Next.js)에서는 **페이지 파일 단위**로 루트 컴포넌트가 다를 수 있다.  
  :contentReference[oaicite:1]{index=1}

## 컴포넌트를 import 하거나 export 하는 방법

- 컴포넌트를 모듈화하여 재사용 가능하도록 하는 과정은 다음 세 단계로 이루어진다:
  1. **새 JS 파일 생성**
  2. **컴포넌트 함수 export**, `default` 또는 `named` 방식 사용
  3. **다른 파일에서 import** (형식에 따라 `default` 또는 `{ named }`)
- 기본 실습 예시:
  - `Gallery.js`에서 `Gallery` 컴포넌트 `export default`
  - `App.js`에서 `import Gallery from './Gallery.js'`  
    :contentReference[oaicite:2]{index=2}

## 한 파일에서 여러 컴포넌트를 import 하거나 export 하는 방법

- 한 파일에 **하나의 default export**만 가능하다.
- **여러 컴포넌트**를 내보내고 싶다면 **named export**를 사용해야 한다.
- 예시:
  - `Gallery.js` 내부에 `export default Gallery` + `export function Profile()`
  - `App.js`에서는 `import Gallery from './Gallery.js'` 그리고 `import { Profile } from './Gallery.js'`  
    :contentReference[oaicite:3]{index=3}

## 요약

- **Root 컴포넌트**는 앱 전체를 구성하는 기준 컴포넌트이다.
- **Export/Import**는 JS 모듈 시스템에서 React 컴포넌트를 분리하고 활용하는 핵심 방법이다.
- **Default export**는 한 파일에 하나만 사용하며, 임포트 시 이름을 자유롭게 지정할 수 있는 유연성을 제공한다.
- **Named export**는 여러 개를 내보낼 수 있으며, **일관적인 이름 유지**와 **IDE 자동완성 / lint 체크** 등의 장점이 있다.  
  :contentReference[oaicite:4]{index=4}

## 인사이트

- 컴포넌트를 파일 단위로 깔끔하게 분리하면 유지보수성과 재사용성이 크게 높아진다.
- `default` 방식은 빠르고 단순하지만, 자유로운 네이밍으로 **협업 시 일관성 관리가 어려울 수 있다**.
- `named` 방식은 코드의 명시성과 안전성(오타 체크, 자동완성 등)이 뛰어나며, 트리 셰이킹 효율에서도 장점이 있다.
- 복잡한 프로젝트에서는 `named export` 중심에 `alias` 제공을 결합하는 방식이 유지보수와 리팩토링에 유리하다.
- 컴포넌트 분리 과정은 React 구조 설계 역량을 키우는 중요한 단계다.
