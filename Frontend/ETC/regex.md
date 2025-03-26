# 정규표현식(Regular Expression) 정리

## 정규표현식이란?

정규표현식(Regex, RegExp)은 **문자열에서 특정 패턴을 검색, 추출, 대체, 검증**할 수 있도록 해주는 표현 방식이다.  
주로 텍스트 파싱, 유효성 검사 등에 사용된다.

---

## 기본 문법

### 1. 리터럴 방식

```js
const regex = /abc/;
```

### 2. 생성자 방식

```js
const regex = new RegExp('abc');
```

---

## 주요 패턴 요소

<!-- prettier-ignore -->
| 패턴 | 의미 |
|-------|------|
| `.`   | 임의의 한 문자 |
| `\d`  | 숫자 (0-9) |
| `\D`  | 숫자가 아닌 문자 |
| `\w`  | 알파벳, 숫자, 밑줄(_) |
| `\W`  | `\w` 이 아닌 것 |
| `\s`  | 공백 문자 (스페이스, 탭 등) |
| `\S`  | 공백이 아닌 문자 |
| `^`   | 문자열의 시작 |
| `$`   | 문자열의 끝 |
| `*`   | 0회 이상 반복 |
| `+`   | 1회 이상 반복 |
| `?`   | 0회 또는 1회 반복 |
| `{n}` | n번 반복 |
| `{n,}`| n번 이상 반복 |
| `{n,m}`| n~m번 반복 |
| `[...]` | 괄호 안 문자 중 하나 |
| `[^...]`| 괄호 안 문자를 제외한 것 |
| `(a\|b)`| a 또는 b |

---

## 플래그(Flags)

| 플래그 | 의미                             |
| ------ | -------------------------------- |
| `g`    | 전역 검색 (global)               |
| `i`    | 대소문자 구분 없음 (ignore case) |
| `m`    | 여러 줄 검색 (multiline)         |

```js
const regex = /abc/gi;
```

---

## 자주 쓰는 예시

### 이메일 검증

```js
const emailRegex = /[a-zA-Z0-9._+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9.]+/;

const emailRegex2 = /^[\w.-]+@[\w.-]+\.[a-zA-Z]{2,}$/;
```

### 숫자만 검사

```js
/^\d+$/;
```

### 휴대폰 번호 (010으로 시작)

```js
/^010-?\d{4}-?\d{4}$/;

/\d{2,3}[- .]\d{3}[- .]\d{4}/gm;
```

### 공백 제거

```js
const trimmed = str.replace(/\s/g, '');
```

### 하이픈 - 제거, 언더바 제거

```js
const raw = `24134---234234--234-2423-
sdgs243--234sfd--333f----2`;

const cleaned = raw.replace(/-/g, '');
console.log(cleaned);

const raw2 = 'asffas--232__sdf--324df__--__---sdfsf__--sdf22-2-3-_';

const cleaned2 = raw.replace(/[-_]/g, '');
console.log(cleaned2);
```

### 숫자만 남기기

```js
raw.replace(/\D/g, '');
```

---

## 유의점 Tip

- `^`와 `$`는 문자열의 **전체 일치 여부**를 판단할 때 사용됨.
- `\`는 이스케이프 문자로 한 번 더 써야 하는 경우가 존재함 (`\\d` 등).

## 정규식 참고 사이트

- 복잡한 정규표현식, 시각적으로 테스트하며 디버깅: [regex101.com](https://regex101.com)
- 유튜브 참고 강의: [정규표현식 , 더이상 미루지 말자 🤩](https://www.youtube.com/watch?v=t3M6toIflyQ)
- 문법 정리: [gitHub](https://github.com/dream-ellie/regex)
- 시각화 연습 사이트: [regexr.com](https://regexr.com/8g1qo)
- 정규식 퀴즈 사이트: [RegexOne](https://regexone.com/)

---

## 마무리

작성한 TIL 을 참고하며 반복 사용해서 익숙해지기.  
정규식 참고용 docs 로 사용.
