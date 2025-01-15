# React, 사전 과제 준비

### 진행할 것

1. 개념정리
2. 실전 프로젝트
3. 개념 복습
4. 공식 Docs
   1. Getting Started 부터 훑어 보기
   2. Main Concepts 집중
   3. Advanced Guides 집중
   4. [Learn 탭](https://react.dev/learn) 읽어보기
   5. API 탭
   6. **Tip**: 달달달 다 외우는 식 공부 X
5. 추가 주요 Topic
   1. Code-Splitting
   1. 규모가 큰 프로젝트에서의 **bundling**
   1. Accessibility
      1. 웹 접근성
   1. Portals
      1. 자식 컴포넌트를 상위에서 보여줄 때
   1. Higher-Order Components
      1. 컴포넌트를 다른 컴포넌트로 wrapping
6. React team Blog issue tracking

### React tool

- [Awesome React Components & Libraries](https://github.com/brillout/awesome-react-components)

##

## React 개념 정리

- JavaScript **Library** for building **user interfaces**
  - Renders **UI** and responds to **events**
  - **\> Components**
- [공식문서 한글버전](https://ko.react.dev/)
- SPA / CSR
  - Single Page Application
  - Client Side Rendering
- \+ Gatsby / \+NEXT.js
  - SSG / SSR
- 좋은 컴포넌트란?
- 독립적 / 고립
- 재사용성이 뛰어남
- 컴포넌트 내부에서는 응집도가 좋음
- 컴포넌트 나누는 기준?
  - 재사용성 DRY
    - Don’t Repeat Yourself
    - 반복하지 말고 재사용 할 것
  - 단일책임 SR
    - Single Responsibility
    - 재사용성은 낮더라도, 한 가지 컴포넌트 내에서 너무 많은 업무를 맡을 때 분리
    - 작은 단위로 나누기
- React Hooks
  - 클래스 컴포넌트의 단점들
    - jS 개발자들에게 복잡한 class
    - this 바인딩 이슈
    - 로직들을 재사용 하기 어려움
      - 상속, 컴포지션 사용해야함
  - hook into
    - 재사용 가능한 로직들
    - react 에서 제공
      - useState / 상태관리 로직
      - useEffect / 컴포넌트 생애주기 관리 로직
      - useRef
      - useMemo
      - useCallback
      - useContext
      - 등등등
    - \+ 우리가 제작한 Custom hook
    - use 로 시작
    - Hooks (함수들은) 값의 재사용이 아닌, **로직의 재사용**을 위한 것

### Setting

- Vite vs CRA
  - [Vite를 사용해야 하는 이유](https://ko.vitejs.dev/guide/why.html)
  - [CRA(Create-React-App) 보다는 Vite 를 사용하는건 어떨까?](https://medium.com/@zero86/cra-create-react-app-%EB%B3%B4%EB%8B%A4%EB%8A%94-vite-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B1%B4-%EC%96%B4%EB%96%A8%EA%B9%8C-eac8c72ec734)
  - [Chat gpt에게 CRA와 Vite에 대해 묻다](https://velog.io/@ebokyung/CRA-vs.-Vite)
  -
- Tools
  - BABEL
    - JavaScript Transcompiler
    - ES2015+ \-\> older version
  - Webpack
    - Bundling the code, JavaScript module bundler
  - ESLint
    - Checking your code
  - Jest
    - Delightful JavaScript **testing** framework
  - PostCSS
    - **Expandable** CSS libraries tool for transforming CSS with JavaScript
  - 유용한 VS Code 익스텐션
    - Material Theme: 현재 사용하고 있는 테마(dark)
    - Material Icon Theme: 현재 사용하고 있는 아이콘들
    - Auto Import: 자동으로 import 해줌
    - Prettier \- Code formatter: 코드를 이쁘게 포맷
    - CSS Modules: 나중에 PostCSS 쓸때 유용함
  - 기타 HTML & CSS 관련 익스텐션
    - IntelliSense for CSS class names in HTML
    - HTML to CSS autocompletion
    - HTML CSS Support
    - CSS Peek
    - Auto Rename Tag
  - 크롬 브라우저
    - [https://www.google.com/chrome/](https://www.google.com/chrome/)
  - 리액트 개발툴 익스텐션
    - [React Developer Tools \- Chrome Web Store](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
