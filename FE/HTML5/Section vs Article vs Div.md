# HTML: section vs article vs div

HTML에서 **`section`**, **`article`**, **`div`** 는 각각의 목적과 의미를 가지고 있으며, 올바르게 사용하는 것이 **SEO(검색 엔진 최적화)** 와 **접근성(A11y)** 측면에서 중요합니다.

---

## 1. section

### 역할

- HTML5에서 추가된 요소로, **문서나 페이지의 주제를 나타내는 의미론적 그룹화**를 위해 사용됩니다.
- **제목(`<h1>~<h6>`)과 함께 사용**하여 그룹의 주제를 나타냅니다.

### 사용 예

- **문서의 논리적인 구역**을 나눌 때.
- 제목과 관련된 내용을 묶어서 구조를 명확히 하고 싶을 때.

### 적합한 상황

- 한 페이지에서 여러 섹션(예: 소개, 기능, 연락처)을 구분할 때.
- 제목과 관련된 내용이 포함된 구역이 필요할 때.

### 예제

```html
<section>
  <h2>About Us</h2>
  <p>We are a team of developers focused on modern web technologies.</p>
</section>

<section>
  <h2>Services</h2>
  <p>We provide web development and design services.</p>
</section>
```

---

## 2. article

### 역할

- HTML5에서 추가된 요소로, **독립적으로 사용 가능한 콘텐츠**를 나타냅니다.
- 다른 곳에 재사용 가능하거나 배포 가능한 콘텐츠에 적합합니다.

### 사용 예

- **독립적인 정보 조각**: 블로그 글, 뉴스 기사, 포럼 게시글, 제품 카드 등.
- 콘텐츠가 독립적으로 의미를 가져야 하는 경우.

### 적합한 상황

- 콘텐츠가 독립적이며, 다른 곳에 재사용되거나 배포될 가능성이 있는 경우.
- 문서의 주요 콘텐츠 항목을 나눌 때.

### 예제

```html
<article>
  <h2>10 Tips for Learning JavaScript</h2>
  <p>JavaScript is one of the most popular programming languages...</p>
</article>

<article>
  <h2>Understanding React Hooks</h2>
  <p>React Hooks allow functional components to manage state and lifecycle...</p>
</article>
```

---

## 3. div

### 역할

- **의미가 없는 컨테이너 요소** 로, 주로 **스타일링(CSS)** 이나 **스크립트(JavaScript)** 작업을 위한 그룹화에 사용됩니다.
- **의미론적인 목적이 없음**.

### 사용 예

- 콘텐츠를 단순히 묶어서 스타일링하거나 레이아웃을 구성할 때.
- 의미가 중요하지 않은 UI 구성 요소.

### 적합한 상황

- 콘텐츠를 그룹화하지만 의미론적인 역할이 필요 없는 경우.
- 레이아웃을 만들거나 스타일 적용을 위한 컨테이너로 사용.

### 예제

```html
<div class="card">
  <h2>Card Title</h2>
  <p>This is some content inside the card.</p>
</div>

<div class="footer">
  <p>Copyright © 2025</p>
</div>
```

---

## 비교

| **특징**          | **section**                    | **article**            | **div**           |
| ----------------- | ------------------------------ | ---------------------- | ----------------- |
| **목적**          | 논리적 섹션 구분               | 독립적인 콘텐츠        | 스타일링/레이아웃 |
| **의미론적 역할** | 있음                           | 있음                   | 없음              |
| **주제**          | 제목과 함께 사용하여 주제 구분 | 독립적으로 의미를 가짐 | 의미 없음         |
| **SEO에 적합**    | 적합                           | 적합                   | 적합하지 않음     |
| **재사용 가능성** | 낮음                           | 높음                   | 없음              |

---

## 적절한 사용 사례

### 1. 섹션을 나누는 경우

```html
<section>
  <h2>Introduction</h2>
  <p>Welcome to our website...</p>
</section>

<section>
  <h2>Features</h2>
  <ul>
    <li>Fast</li>
    <li>Secure</li>
  </ul>
</section>
```

### 2. 독립적인 콘텐츠

```html
<article>
  <h2>Blog Post 1</h2>
  <p>Details about the first blog post...</p>
</article>

<article>
  <h2>Blog Post 2</h2>
  <p>Details about the second blog post...</p>
</article>
```

### 3. 단순 레이아웃 및 스타일링

```html
<div class="grid-container">
  <div class="grid-item">Item 1</div>
  <div class="grid-item">Item 2</div>
  <div class="grid-item">Item 3</div>
</div>
```

---

## 결론

- **`section`**: 문서나 페이지의 주제를 나누고 그룹화할 때 사용.
- **`article`**: 독립적인 콘텐츠(블로그, 뉴스 기사 등) 작성에 사용.
- **`div`**: 스타일링 및 레이아웃을 위한 컨테이너로 사용.

페이지 구조를 설계할 때 **`section`** 과 **`article`** 을 우선적으로 고려하고, 의미가 없는 단순한 그룹화가 필요할 때만 **`div`** 를 사용하는 것이 좋습니다.
