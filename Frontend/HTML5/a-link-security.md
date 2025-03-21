# 🔐 \[TIL] target="\_blank"와 rel="noopener noreferrer"의 보안 관계

외부 링크를 `<a href target="_blank">`로 여는 경우,

`rel="noopener noreferrer"`를 반드시 같이 사용하는 이유에 대한 학습.
속성 누락이 **사용자 보안에 심각한 위협**을 줄 수 있음.

---

## ✅ 문제 상황

```html
<a href="https://external-site.com" target="_blank">외부 링크</a>
```

이렇게만 작성하면 새 탭으로 링크를 열 수 있다. 그러나 **보안적으로 심각한 취약점**이 발생한다:

1. `window.opener`을 통한 **탭 탈취(Tabnabbing)** 공격 가능
2. 브라우저가 자동으로 `Referer` 헤더를 전송해 **사용자의 현재 URL 노출** 가능

---

## ⚠️ 주요 취약점

### 1. Tabnabbing 공격

- `target="_blank"`로 열리는 새 탭은 부모 탭에 접근할 수 있다 (`window.opener`)
- 악성 사이트는 자바스크립트로 부모 탭의 URL을 마음대로 변경할 수 있다

#### 예시 코드:

```js
// 악성 사이트 내부 코드
setTimeout(() => {
  window.opener.location.href = 'https://fake-login.com';
}, 2000);
```

> 사용자가 원래 탭으로 돌아왔을 때 피싱 사이트로 바뀌어 있음

### 2. Referer 정보 노출

- 기본적으로 브라우저는 외부로 이동할 때 `Referer` 헤더에 현재 페이지의 URL을 담음
- 비공개 토큰, 이메일 등 민감한 정보가 담긴 URL인 경우 심각한 정보 유출 발생

#### 예시:

```html
<!-- 현재 페이지: https://mybank.com/reset-password?token=ABC123 -->
<a href="https://support-site.com" target="_blank">도움말</a>
```

> `support-site.com`은 해당 토큰을 헤더로 받게 됨

---

## ✅ 해결 방법

```html
<a href="https://external-site.com" target="_blank" rel="noopener noreferrer"> 외부 링크 </a>
```

| 속성         | 설명                                               |
| ------------ | -------------------------------------------------- |
| `noopener`   | 새 탭에서 `window.opener` 접근 차단 → 탭 탈취 방지 |
| `noreferrer` | Referer 헤더 제거 → 개인정보 유출 방지             |

---

## 🛡 ESLint 설정 (Next.js 기준)

Next.js + ESLint 환경이라면, 아래 룰을 통해 자동 감지를 설정할 수 있다:

```json
"rules": {
  "react/jsx-no-target-blank": ["error", { "allowReferrer": false }]
}
```

> `target="_blank"`가 있는데 `rel="noopener noreferrer"`가 빠진 경우 경고 발생

---

## 💡 결론

- `target="_blank"`만 사용하면 사용자 탭이 **해킹당할 수 있다**.
- 실무에서는 항상 `rel="noopener noreferrer"`를 함께 써야 한다.
- 특히 OAuth, 민감한 페이지일수록 `noreferrer`는 더더욱 필수다.

앞으로 외부 링크를 열 땐 **보안을 전제로 작성**하자. 작은 속성이지만 큰 사고를 막는다.

---

### 참고 링크

- [MDN Docs - rel Attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel)
- [OWASP - Tabnabbing 설명](https://owasp.org/www-community/attacks/Reverse_Tabnabbing)
