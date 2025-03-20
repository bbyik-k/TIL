# TIL: `fs.readFileSync` vs `await fs.promises.readFile`

Node.js에서 파일을 읽을 때 가장 자주 쓰이는 API는 `fs.readFileSync`와 `fs.promises.readFile`이다.
Next.js 프로젝트에서 서버 컴포넌트나 API Route에서 파일 데이터를 읽을 일이 많아지면서,
이 둘의 차이와 적절한 사용 타이밍을 명확히 정리하고자 한다.

---

## ✅ 개요: 두 방식의 핵심 차이

| 항목        | `fs.readFileSync`                      | `await fs.promises.readFile`         |
| ----------- | -------------------------------------- | ------------------------------------ | ------- |
| 처리 방식   | 동기 (Blocking)                        | 비동기 (Non-blocking)                |
| 반환값      | 문자열 또는 버퍼                       | Promise<string                       | Buffer> |
| 사용 시기   | 빠른 스크립트, 작은 파일, 초기 설정 등 | 대부분의 비동기 환경, 성능 중요할 때 |
| 코드 스타일 | 간단하지만 블로킹                      | 현대적, 깔끔하고 안전 ✅             |

---

## 📦 사용 방법

### 1. `fs.readFileSync`

```ts
import fs from 'fs';

const data = fs.readFileSync('data/posts.json', 'utf-8');
const posts = JSON.parse(data);
```

- 장점: 코드가 단순하고 빠름
- 단점: **처리 중 다른 요청을 블로킹**함 (Node.js는 단일 스레드이므로 주의)

---

### 2. `await fs.promises.readFile`

```ts
import {{ promises as fs }} from 'fs';

export async function getPosts() {
  const data = await fs.readFile('data/posts.json', 'utf-8');
  return JSON.parse(data);
}
```

- 장점: 논블로킹으로 성능 우수, `await` 문법과 잘 어울림
- 단점: `async` 함수로 작성해야 하므로 상위 구조가 필요

---

## ✅ 실무에서의 선택 기준

| 상황                       | 추천 방식                                     |
| -------------------------- | --------------------------------------------- |
| JSON 등의 정적 데이터 파일 | `readFileSync` (단, 작고 자주 바뀌지 않을 때) |
| Next.js 서버 컴포넌트 내부 | 둘 다 사용 가능 (작은 파일이면 `sync` OK)     |
| API Route, Dynamic Route   | ✅ `await fs.promises.readFile` 권장          |
| 병렬/동시 요청 가능성 있음 | ✅ 비동기 방식 필수                           |

---

## 💡 실제 Next.js 서비스에서 활용 예

### `service/posts.ts`

```ts
import path from 'path';
import {{ promises as fs }} from 'fs';

export async function getPosts() {
  const filePath = path.join(process.cwd(), 'data', 'posts.json');
  const file = await fs.readFile(filePath, 'utf-8');
  return JSON.parse(file);
}
```

### 사용 위치: `/app/posts/page.tsx`

```ts
import {{ getPosts }} from '@/service/posts';

export default async function PostsPage() {
  const posts = await getPosts();
  return <ul>{posts.map(p => <li key={p.id}>{p.title}</li>)}</ul>;
}
```

---

## ✨ 결론

- 단순하고 빠른 코드엔 `fs.readFileSync()`도 OK
- Next.js나 실무 환경에서는 대부분 `await fs.promises.readFile()` 방식 추천
- 블로킹을 피하고, 유지보수성과 병렬처리 대응력을 높일 수 있음

---

## 🔗 참고 문서

- [Node.js fs 공식 문서](https://nodejs.org/api/fs.html)
- [Next.js App Router 서버 컴포넌트](https://nextjs.org/docs/app/building-your-application/rendering/server-components)

---

> 파일 시스템 작업도 결국은 사용 목적과 환경에 따라 유연하게 선택하는 것이 중요하다.
> 성능, 코드 스타일, 그리고 병목 여부까지 고려한 똑똑한 선택이 필요하다.
