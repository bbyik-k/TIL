# Static Routing vs Dynamic Routing 정리

### ✅ 개요

Next.js 13의 **App Router** 환경에서는 기존 `pages/` 라우팅 방식과 달리,  
파일 시스템을 적극 활용한 **폴더 기반의 정적/동적 라우팅**이 도입 되었습니다.
Next.js 13에서의 **Static Routing**과 **Dynamic Routing**의 개념, 구현 원리, 장단점을 정리합니다.

---

## Static Routing (정적 라우팅)

### 📌 정의

- **경로가 고정된 라우트**
- 특정한 파일 이름과 폴더 구조에 따라 URL이 정해짐

### 📁 예시 구조

```
app/
├── about/
│   └── page.tsx     → /about
├── contact/
│   └── page.tsx     → /contact
```

### ✅ 특징

- 컴파일 시점에 경로가 정해짐
- SEO 친화적이며 빠른 응답
- 빌드시 HTML 생성 가능 (SSG)

### ✅ 장점

- 빠른 초기 로딩
- 명확한 경로 구조 → 유지보수 용이
- 정적 최적화 가능 (`generateStaticParams`, `revalidate` 활용 시 ISR도 가능)

### ❌ 단점

- 경로 수가 많아질수록 모든 조합을 미리 만들어야 하므로 빌드 시간이 길어짐
- 유저 맞춤형 동적 콘텐츠 제공이 어려움

---

## Dynamic Routing (동적 라우팅)

### 📌 정의

- **파일 또는 폴더 이름에 대괄호를 사용하여 변수처럼 경로를 지정**

### 📁 예시 구조

```
app/
└── products/
    └── [slug]/
        └── page.tsx   → /products/:slug
```

- URL 예: `/products/shoes`, `/products/shirt`

### ✅ 특징

- 경로 일부를 변수처럼 받아 처리함
- 페이지 진입 시 `params`를 통해 값 사용
- `generateStaticParams`를 통해 SSG로도 사용 가능

### ✅ 장점

- 경로를 템플릿화하여 재사용성 증가
- 상품 상세 페이지, 블로그 포스트 등에 적합
- SSG + SSR + ISR 등 다양한 렌더링 전략과 함께 사용 가능

### ❌ 단점

- 파일 시스템 기반이라 경로가 명시적으로 보이지 않아 유지보수 시 혼란 가능
- 잘못된 URL 접근 시 에러 처리를 별도로 해야 함
- `generateStaticParams` 없이 접근하면 404 또는 SSR 처리로 전환됨

---

## 핵심 비교 정리

| 항목          | Static Routing                      | Dynamic Routing                          |
| ------------- | ----------------------------------- | ---------------------------------------- |
| 경로 구성     | 폴더 이름이 경로                    | `[param]` 형식으로 변수 처리             |
| URL 결정 시점 | 컴파일 타임                         | 런타임 또는 컴파일 시                    |
| SEO           | 매우 우수                           | 적절히 설정 시 우수                      |
| 사용 예       | 소개, 홈, 카테고리 등 고정 페이지   | 상품, 게시글, 사용자 상세 페이지         |
| 확장성        | 낮음 (모든 경로를 직접 만들어야 함) | 높음 (동적 파라미터로 수천 개 처리 가능) |
| 빌드 최적화   | 쉽고 빠름                           | `generateStaticParams` 필요 시 복잡도 ↑  |

---

## ✍️ 개인 인사이트

Next.js 13에서 정적/동적 라우팅은 **파일 기반 라우팅의 진화된 형태**입니다.

특히 동적 라우팅이 단순히 "slug 처리"만 가능한 것이 아니라,  
`generateStaticParams`, `revalidate`, `notFound()` 등과 조합하여  
**SSR, ISR, 클라이언트 fallback까지 다양한 전략을 구현**할 수 있다는 점이 인상적입니다.

App Router를 사용하는 지금, 라우팅 전략은 단순히 폴더 구조를 짜는 일이 아니라,  
**UX + 성능 + SEO + 유지보수성까지 고려한 설계 행위**가 되었습니다.

프론트엔드에서의 역할이 점점 커지고, 이에 따라 아키텍처 설계 및 디자인에 대한 고민과 필요성이 높아지는 것이 느껴집니다.

---

## 🔗 참고 문서

- [Next.js 공식 라우팅 문서](https://nextjs.org/docs/app/building-your-application/routing)
- [Dynamic Routes](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)
- [generateStaticParams 가이드](https://nextjs.org/docs/app/api-reference/functions/generate-static-params)

---

### 📚Todo

- `catch-all`
- `optional-catch-all`
- 병렬 라우팅
