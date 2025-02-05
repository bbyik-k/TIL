# Git Flow에서 Feature 브랜치 나누는 기준

## 1. 기본적인 Feature 브랜치 분리 원칙

Git Flow에서 feature 브랜치를 나눌 때는 다음 기준을 따르는 것이 좋습니다:

1. **각 feature 브랜치는 하나의 주요 기능(Feature) 단위를 담당해야 함**

   - 한 feature 브랜치에서 너무 많은 기능을 구현하면 코드 리뷰나 병합이 어려워짐.
   - 반대로 너무 세부적으로 나누면 관리가 복잡해지고 merge 충돌이 많아질 수 있음.

2. **독립적으로 테스트 가능한 단위로 나누기**
   - 기능 개발이 완료되면 `develop`에 병합할 수 있어야 함.
   - 관련된 기능이 하나의 feature 브랜치에서 함께 개발되도록 조정.

---

## 2. Ex. 유튜브 클론 프로젝트에서 적절한 Feature 브랜치 나누기

초기 상태라면, **페이지 단위 → 기능 단위 → 세부 기능 단위**로 점진적으로 나누는 것이 좋습니다.

### (1) 초기 프로젝트 세팅

```bash
git flow feature start setup-project
```

- 프로젝트 초기 세팅, 라이브러리 설치 등
- Tailwind CSS, React Router, Firebase, TanStack Query 등 초기 구성
- 세팅 후 `develop` 브랜치에 병합

---

### (2) 페이지 단위 Feature 브랜치

#### 1️⃣ 메인 페이지 (홈 화면)

```bash
git flow feature start main-page
```

- `/` 경로에 해당하는 기본적인 메인 페이지 생성
- 페이지 내 주요 섹션을 구성하는 컴포넌트 기본 뼈대 작성

#### 2️⃣ 비디오 상세 페이지

```bash
git flow feature start video-detail-page
```

- `/video/:id` 경로 생성 및 기본적인 레이아웃 작업

---

### (3) 주요 UI 요소 및 기능 단위 Feature 브랜치

#### 1️⃣ 네비게이션 바(Navbar)

```bash
git flow feature start navbar
```

- 상단 Navbar UI 구성
- 로그인 상태에 따른 UI 처리

#### 2️⃣ 검색 기능 (Search)

```bash
git flow feature start search
```

- Navbar에 검색창 추가
- 검색 API 연동 및 결과 페이지 생성

#### 3️⃣ 다크 모드 기능 (Dark Mode)

```bash
git flow feature start dark-mode
```

- 테마 상태 전환 기능 추가
- Tailwind 및 Context API 사용하여 다크모드 설정

---

### (4) 데이터 Fetching 관련 Feature 브랜치

#### 1️⃣ 유튜브 API 연동 및 동영상 리스트 표시

```bash
git flow feature start fetch-videos
```

- TanStack Query + Axios를 사용하여 동영상 목록 불러오기
- 비디오 썸네일을 표시하는 VideoCard 컴포넌트 생성

#### 2️⃣ 추천 비디오 섹션 (Related Videos)

```bash
git flow feature start related-videos
```

- `/video/:id` 페이지에서 관련 비디오 목록 표시
- 추천 API 연동

#### 3️⃣ 채널 정보 가져오기 (Channel Info)

```bash
git flow feature start channel-info
```

- `/video/:id`에서 채널 정보 표시
- 구독 여부 확인 및 UI 구성

---

### (5) 장바구니 또는 좋아요 기능 같은 부가 기능

(유튜브 클론 프로젝트에서는 주 기능이 아니지만, 필요하다면 추가 가능)

#### 1️⃣ 좋아요/싫어요 기능

```bash
git flow feature start like-feature
```

- 좋아요/싫어요 버튼 추가
- Firebase 또는 API 연동하여 상태 저장

#### 2️⃣ 구독 기능

```bash
git flow feature start subscribe
```

- 채널 구독/구독 취소 기능 추가
- Firebase 또는 API 연동

---

## 3. 정리: Feature 브랜치 생성 단위

✅ **큰 범위에서 시작하여 점점 세부적으로 나누기**  
✅ **독립적으로 개발할 수 있는 단위로 브랜치를 생성**  
✅ **작업이 너무 세부적으로 나누어지면 합쳐서 작업**  
✅ **각 feature 브랜치는 단일 기능을 목표로 함**

### 최종 브랜치 구조 예시

```
develop
 ├── feature/setup-project
 ├── feature/main-page
 ├── feature/video-detail-page
 ├── feature/navbar
 ├── feature/search
 ├── feature/dark-mode
 ├── feature/fetch-videos
 ├── feature/related-videos
 ├── feature/channel-info
 ├── feature/like-feature
 ├── feature/subscribe
```

이렇게 나누면 Git Flow를 효율적으로 활용하면서 기능 개발을 체계적으로 관리할 수 있습니다.  
필요한 경우 더 세부적으로 나누거나, 여러 작은 기능을 합쳐서 개발할 수도 있습니다. 🚀😊

## Todo

SOLID 원칙 등, 프론트엔드 분야의 아키텍처에 대하여 더욱 공부해 보고 개념을 적립하는 것이 필요해 보인다.  
이후 다시 이 기준을 다시 살펴보고, 개선해 가자.
