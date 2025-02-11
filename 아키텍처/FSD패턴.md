# 프론트엔드에서의 FSD(Feature-Sliced Design) 아키텍처 패턴

## 🚀 1. FSD(Feature-Sliced Design)이란?

FSD(Feature-Sliced Design)은 **프롬트엔드 애플리케이션을 기능(feature) 단위로 나누어 관리하는 아키템플렉차 패턴**입니다.  
기존의 **레이어드 아키템플렉차**(예: `components/`, `hooks/`, `utils/`)는 군데가 커지는 보험성이 있지만, 워크플로우의 크기가 증가할수록 유지보수가 어려워지는 문제를 걸친다.  
FSD는 **"기능 중심"** 구조로 조정을 나누어 프로젝트를 확장 가능하도록 합니다.

---

## 📂 2. 기본 구조

FSD는 **기능 중심의 계층적 구조**를 따름니다.  
예제: `src/` 디렉토리 구조 (React 기반)

```
📾 src
 ┗📂 app              # 앱 전역 설정 (라우팅, 상태 관리, API 설정 등)
 ┗📂 entities         # 해당 단위의 타깃지 (ex. User, Product 등)
 ┗📂 features         # 기능 단위 모듈 (ex. 로그인, 상품 조회 등)
 ┗📂 widgets         # 보횜 컨퍼넌트 (ex. Navbar, Sidebar)
 ┗📂 pages           # 페이지 단위 컨퍼넌트
 ┗📂 shared          # 공통적으로 사용되는 컨퍼넌트 및 유틸
```

---

## ⚡ 3. 적용 예시 (React + FSD)

### 📌 로그인 기능 (`features/auth/`)

#### 📂 폴더 구조

```
📾 src/features/auth
 ┗📂 ui
 ┗📂 model
 ┗📂 api
```

#### 📌 `authAPI.js` (API 요청 관리)

```js
export async function loginUser(credentials) {
  const response = await fetch('/api/login', {
    method: 'POST',
    body: JSON.stringify(credentials),
    headers: { 'Content-Type': 'application/json' },
  });
  return response.json();
}
```

---

## ✅ 4. FSD의 장점 & 단점

### **🔹 장점**

✅ **기능 중심의 구조 → 유지보수 우유**  
✅ **확장성**  
✅ **코드 견디성 증가**  
✅ **팀 개발에 적합**

### **🔸 단점**

❌ **초기 도입 불만**  
❌ **작은 프로젝트에서는 과도할 수 있음**  
❌ **명확한 규칙 필요**

---

## 🏆 5. FSD 도입 시 고려할 점

- 프로젝트 크기가 **중간~대형까지**라면 보험성 증가.
- 팀 내에서 **폴더 구조 및 역할을 명확히 정해**야 함.

---

## 🎯 6. 결론

FSD(Feature-Sliced Design) 아키템플렉차는 프로젝트를 **기능 단위로 구조하여 확장성과 유지보수를 높이는 패턴**입니다.
