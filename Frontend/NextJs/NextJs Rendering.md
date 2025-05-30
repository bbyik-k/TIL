# CSR / SSG / ISR / SSR + 하이브리드 / 하이드레이션 개념 및 활용

## 1. CSR (Client-Side Rendering)

### ✅ 개념

- 클라이언트(브라우저)에서 JavaScript를 실행하여 페이지를 렌더링하는 방식
- 초기 HTML은 최소한의 마크업만 포함하고, React와 같은 프레임워크가 동적으로 콘텐츠를 렌더링
- API 요청 후 데이터를 받아와 화면을 구성

### ✅ 장점

- 첫 요청 시에는 가벼운 HTML을 로드하므로 서버 부담이 적음
- 페이지 간 이동이 빠름 (클라이언트가 처리)
- 캐싱이 쉬움 (CDN 활용 가능)

### ✅ 단점

- **SEO에 불리**: 크롤러가 데이터를 읽기 전에 JavaScript가 실행되어야 함
- **초기 로딩 속도 느림**: 브라우저가 JavaScript를 다운로드하고 실행하는 데 시간이 필요함
- **JS 실행을 지원하지 않는 환경에서 작동 어려움**

### ✅ 언제 사용하면 좋을까?

- SPA(Single Page Application)
- 로그인 후 내부 대시보드처럼 SEO가 중요하지 않은 페이지
- 사용자 인터랙션이 많고 실시간 데이터를 다뤄야 하는 애플리케이션 (예: 채팅, 대시보드)

---

## 2. SSG (Static Site Generation)

### ✅ 개념

- 빌드 시 HTML을 생성하여 정적 파일로 배포하는 방식
- 모든 페이지가 미리 생성되어 있어 서버 부하가 적음
- Next.js의 `getStaticProps()`와 `getStaticPaths()`를 활용하여 구현 가능

### ✅ 장점

- **SEO 최적화**: 크롤러가 완전한 HTML을 바로 읽을 수 있음
- **초기 로딩 속도 빠름**: 정적 파일을 직접 제공하므로 빠르게 렌더링 가능
- **서버 부하 적음**: 요청마다 서버에서 처리할 필요 없이 CDN에서 바로 제공 가능

### ✅ 단점

- **실시간 데이터 반영 어려움**: 빌드 시점 이후의 데이터 변경을 반영하려면 빌드를 다시 해야 함
- **페이지 개수가 많으면 빌드 시간이 길어짐**

### ✅ 언제 사용하면 좋을까?

- 블로그, 문서 사이트, 마케팅 페이지 (변경이 적고 SEO가 중요한 경우)
- 제품 소개 페이지 (데이터 변경이 적고 빠른 로딩이 중요한 경우)

---

## 3. ISR (Incremental Static Regeneration)

### ✅ 개념

- SSG의 단점을 보완한 방식으로, 일정 주기마다 페이지를 재생성하여 변경된 데이터를 반영
- Next.js의 `revalidate` 옵션을 사용하여 특정 간격으로 정적 페이지를 다시 생성

### ✅ 장점

- **SEO 최적화** + **실시간 데이터 반영 가능**
- **빌드 속도 최적화**: 모든 페이지를 빌드할 필요 없이 필요할 때만 다시 생성

### ✅ 단점

- **즉시 데이터가 변경되지 않음**: 변경이 반영되기까지 캐시 유지 시간만큼의 지연 발생
- **첫 요청 시 구버전이 제공될 가능성 있음**

### ✅ 언제 사용하면 좋을까?

- 제품 리스트 페이지 (자주 변경되지만 완전한 실시간성은 필요 없는 경우)
- 뉴스, 블로그 등 비교적 정적인 콘텐츠지만 가끔 업데이트가 필요한 경우

---

## 4. SSR (Server-Side Rendering)

### ✅ 개념

- 서버에서 요청을 받을 때마다 HTML을 생성하여 반환하는 방식
- Next.js의 `getServerSideProps()`를 활용하여 구현 가능

### ✅ 장점

- **SEO 최적화**: 요청 시마다 최신 데이터를 포함한 HTML을 생성하여 반환
- **실시간 데이터 반영 가능**: 항상 최신 데이터를 제공

### ✅ 단점

- **서버 부하 증가**: 요청이 있을 때마다 HTML을 생성하므로 서버 비용 증가 가능
- **초기 로딩 속도 느림**: 요청을 처리한 후 HTML을 반환하므로 지연 발생 가능

### ✅ 언제 사용하면 좋을까?

- 로그인 필요 페이지 (사용자별 데이터를 받아야 하는 경우)
- 대시보드 (항상 최신 데이터를 가져와야 하는 경우)
- API에서 자주 변경되는 데이터를 가져와야 하는 경우

---

## 5. 하이브리드 렌더링

### ✅ 개념

- SSG, ISR, SSR, CSR을 혼합하여 필요에 맞게 최적화하는 방식

### ✅ 예제

- 정적 콘텐츠 (블로그, 마케팅 페이지) → **SSG**
- 자주 업데이트되는 콘텐츠 (상품 리스트) → **ISR**
- 로그인 필요 페이지 → **SSR**
- 실시간 대시보드 → **CSR**

### ✅ 언제 사용하면 좋을까?

- 다양한 요구사항이 섞인 웹사이트 (e.g. e-commerce, SaaS 대시보드 등)

---

## 6. 하이드레이션 (Hydration)

### ✅ 개념

- 서버에서 렌더링된 HTML을 클라이언트에서 React가 takeover(재활성화)하는 과정

### ✅ 장점

- SSR을 이용한 초기 렌더링 속도 개선 가능
- 클라이언트 사이드에서 React가 기능을 추가하며 인터랙션을 담당

### ✅ 단점

- 클라이언트에서 JS를 로드하고 React가 실행되기까지의 지연 발생 (hydration delay)
- 무거운 JS를 실행하는 경우 성능 저하 가능

### ✅ 언제 사용하면 좋을까?

- SSR 기반 웹 애플리케이션에서 사용자 인터랙션이 많은 경우
- 빠른 초기 로딩이 필요하지만 CSR의 동적 기능도 활용해야 하는 경우

---

## 7. 언제 어떤 렌더링 방식을 선택해야 할까?

| 요구사항                                 | 적절한 렌더링 방식 |
| ---------------------------------------- | ------------------ |
| SEO가 중요한 블로그, 문서 사이트         | **SSG**            |
| 제품 리스트, 뉴스 페이지 (자주 업데이트) | **ISR**            |
| 로그인 필요 페이지 (사용자 데이터 반영)  | **SSR**            |
| 실시간 데이터 대시보드, 채팅             | **CSR**            |
| 다양한 요구사항이 섞인 사이트            | **하이브리드**     |

---

### 🔥 **결론**

- **SSG**: 빠른 로딩 & SEO 필요할 때
- **ISR**: 정적 사이트지만 업데이트 필요할 때
- **SSR**: 실시간 데이터 & 사용자별 콘텐츠 필요할 때
- **CSR**: 상호작용 많고 API로 데이터 받아오는 앱
- **하이브리드**: 여러 방식을 섞어 최적화
