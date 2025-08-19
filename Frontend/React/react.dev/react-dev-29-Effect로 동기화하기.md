# React.dev-docs-29-Effect로 동기화하기

[REACT 학습하기 > 탈출구 > Effect로 동기화하기](https://ko.react.dev/learn/synchronizing-with-effects)

## Goal

일부 컴포넌트는 외부 시스템과 동기화해야 한다.  
예를 들어 React의 state를 기준으로 React가 아닌 위젯을 제어하거나, 서버 연결을 설정하거나, 화면 노출 시 분석 로그를 전송할 수 있다.  
Effect를 사용하면 **렌더링 이후** 특정 코드를 실행하여 컴포넌트와 외부 세계(브라우저/네트워크/타사 API)를 동기화한다.

- Effect가 무엇인지 정리한다.
- Effect가 이벤트와 어떻게 다른지 설명한다.
- 컴포넌트에서 Effect를 올바르게 선언하는 법을 숙지한다.
- 불필요한 Effect 재실행을 건너뛰는 법을 학습한다.
- 개발 모드에서 Effect가 두 번 실행되는 이유와 대처법을 이해한다.

## Effect란 무엇이고 이벤트와는 어떻게 다른가요?

- **이벤트 핸들러**는 사용자의 상호작용에 의해 실행된다(버튼 클릭, 입력 변경 등). 렌더 중 계산과 UI 갱신 로직을 다룬다.
- **Effect**는 **렌더링 그 자체**에 의해 실행된다. React가 DOM을 커밋한 **이후** 외부 시스템과 동기화(구독, DOM 측정/제어, 네트워크 요청, 콘솔/로그)를 수행한다.
- 이벤트는 “무엇이 일어났을 때 실행”, Effect는 “무엇이 렌더되었을 때 실행”으로 이해한다.

## Effect가 필요 없을지도 모릅니다

Effect는 “탈출구”이므로 남용하지 않는다. 다음과 같은 경우는 Effect 없이 해결한다.

- **파생 값 계산**: props/state로부터 계산 가능한 값은 **렌더 중 계산**한다(`useMemo`조차 불필요한 경우가 많다).
- **상태 초기화/리셋**: 특정 키가 바뀔 때 상태를 재설정해야 하면 **`key`를 변경**해 컴포넌트를 재마운트한다.
- **이벤트 기반 로직**: 사용자 행동에 반응하는 동작은 **이벤트 핸들러**에 둔다(예: “구매하기” 버튼).
- **Ref로 최근 값 유지**: 렌더를 원치 않는 값은 **`useRef`** 에 둔다(타이머 ID, 외부 인스턴스, 최신 핸들러 등).

## Effect를 작성하는 법

### 1단계: Effect 선언하기

- `useEffect(setup)` 형태로 선언하며, 기본적으로 **모든 렌더 후** 실행된다.
- Effect 내부에서 외부 세계와 동기화하고, 필요 시 정리 함수를 반환한다.

```tsx
import { useEffect } from 'react';

function TitleSync({ title }: { title: string }) {
  useEffect(() => {
    document.title = title; // 동기화
    return () => {
      /* 정리 필요 없음 */
    };
  }, [title]);
  return null;
}
```

> **주의하세요!**  
> `useEffect` 내부에서 **조건 없이 `setState`** 를 호출하고 의존성 배열을 잘못 지정하면 **무한 렌더 루프**를 만든다. Effect는 “렌더 후” 실행되므로, 다음 렌더를 유발하는 코드라면 **의존성**을 정확히 설정하거나 설계를 재검토해야 한다.

> **ref 콜백을 사용하여 ref 리스트 관리하기**  
> DOM 노드를 Effect에서 선택하려고 `querySelector`를 남발하기보다, **콜백 ref** 로 필요한 노드들을 수집하고(마운트/언마운트에 맞춰 콜백이 호출됨) Effect에서 그 참조를 사용한다. 이렇게 하면 **의존성 관리가 단순**해지고, 레이스 컨디션을 줄일 수 있다.

### 2단계: Effect의 의존성 지정하기

- 두 번째 인자로 **의존성 배열**을 전달해 재실행 시점을 제어한다. React는 의존성 값이 **지난 렌더와 같으면** Effect를 건너뛴다.

```tsx
useEffect(() => {
  const id = setInterval(tick, delay);
  return () => clearInterval(id);
}, [delay]); // delay가 변할 때만 재설치
```

> **주의하세요!**  
> 의존성 배열을 생략하면(두 번째 인자 없음) **모든 렌더 후** 실행된다. 빈 배열 `[]`은 **마운트 시 한 번**(그리고 언마운트 시 정리) 실행을 의미한다. 의미가 다르므로 주의한다.

> **주의하세요!**  
> 의존성은 **선택사항이 아니다**. Effect 내부에서 사용하는 **반응형 값**(props, state, 컨텍스트, 다른 훅이 반환한 값/함수)은 모두 의존성에 포함되어야 한다. ESLint의 `react-hooks/exhaustive-deps` 규칙을 사용해 누락을 방지한다.

> **왜 ref는 의존성 배열에서 생략해도 되나요?**  
> `ref.current`는 **가변이지만 반응형이 아닌 값**이다. 변경해도 렌더를 트리거하지 않으므로, 의존성에 포함해도 재실행 기준으로 의미가 없다. 대신 **읽기 시점의 값**이 중요하다면 Effect 안에서 읽고, “최신 콜백”이 필요하면 `useEvent`(또는 ref 동기화) 패턴을 사용한다.

### 3단계: 필요하다면 클린업을 추가하세요

- Effect가 구독/타이머/외부 인스턴스를 설치했다면 **정리 함수**를 반환하여 **다음 Effect 실행 전**과 **언마운트 시** 해제한다.

```tsx
useEffect(() => {
  const controller = new AbortController();
  fetch(url, { signal: controller.signal });
  return () => controller.abort(); // 정리
}, [url]);
```

## 개발 중에 Effect가 두 번 실행되는 경우를 다루는 방법

- 개발 모드의 **Strict Mode**는 버그 탐지를 위해 **마운트 → 언마운트 → 재마운트**를 즉시 시뮬레이션한다. 이 과정에서 Effect가 두 번 실행되는 것처럼 보인다.
- 정리 함수가 정확하면 문제 없다. **이벤트 리스너/타이머/구독**을 중복 등록하지 않도록 **정리에서 해제**한다.

> **주의하세요!**  
> Effect가 두 번 실행되는 것을 피하려고 **ref 플래그로 “한 번만 실행”을 강제**하지 않는다. 이는 실제 환경(코드 스플릿, 탭 전환 복원 등)에서의 재마운트/재실행 시나리오를 가린다. 올바른 해법은 **정리 함수를 정확히 구현**하는 것이다.

### React로 작성되지 않은 위젯 제어하기

- 외부 위젯(Map/Chart/Video 등)을 **마운트 시 생성하고 언마운트 시 파괴**한다.
- 옵션 변화에 따라 **최소 의존성**으로 업데이트하거나, 불변 옵션은 생성 시 한 번만 넘긴다.

```tsx
function MapView({ center, zoom }) {
  const containerRef = useRef<HTMLDivElement>(null);
  const mapRef = useRef<any>(null);

  useEffect(() => {
    if (!containerRef.current) return;
    mapRef.current = new MapSDK.Map(containerRef.current, { center, zoom });
    return () => mapRef.current?.destroy();
  }, []);

  useEffect(() => {
    mapRef.current?.setView(center, zoom);
  }, [center, zoom]);

  return <div ref={containerRef} style={{ height: 400 }} />;
}
```

### 이벤트 구독하기

- 구독은 `addEventListener` 또는 SDK의 `subscribe`로 설치하고, 정리에서 해제한다.
- 최신 핸들러를 보장하려면 **`useEvent`**(React 19) 또는 **ref 동기화** 패턴을 사용한다.

```tsx
function useWindowResize(onResize: () => void) {
  const handler = useEvent(onResize); // React 19
  useEffect(() => {
    const cb = () => handler();
    window.addEventListener('resize', cb);
    return () => window.removeEventListener('resize', cb);
  }, []);
}
```

### 애니메이션 트리거

- DOM 기반 애니메이션(Web Animations API 등)은 **마운트 후 트리거**하고, 필요 시 정지/취소한다.
- 레이아웃 측정과 즉시 스타일 적용이 필요하면 `useLayoutEffect`를 고려한다.

```tsx
function FadeIn({ children }: { children: React.ReactNode }) {
  const ref = useRef<HTMLDivElement>(null);
  useEffect(() => {
    const el = ref.current;
    if (!el) return;
    const anim = el.animate([{ opacity: 0 }, { opacity: 1 }], { duration: 300 });
    return () => anim.cancel();
  }, []);
  return <div ref={ref}>{children}</div>;
}
```

### 데이터 페칭

- Effect에서의 페칭은 **클라이언트 전용 앱**에서는 유효하나, 중복 요청/깜빡임/캐시 부재 등 단점이 있다. **AbortController**로 중단을 처리하고, 의존성에 `url` 등 **정확한 키**를 사용한다.

```tsx
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<Error | null>(null);
  useEffect(() => {
    const ac = new AbortController();
    setData(null);
    setError(null);
    fetch(url, { signal: ac.signal })
      .then((r) => r.json())
      .then(setData, (e) => {
        if (e.name !== 'AbortError') setError(e);
      });
    return () => ac.abort();
  }, [url]);
  return { data, error };
}
```

> **Effect에서 데이터를 가져오는 좋은 대안은 무엇인가요?**  
> Effect 내부 `fetch`는 제어가 수동적이며 중복/경합/캐시 문제를 직접 해결해야 한다. **권장 대안**은 다음과 같다.
>
> - **프레임워크 데이터 로딩**: Next.js Route Handlers/Server Components, Remix Loaders 등으로 **서버에서 선로딩**한다.
> - **전용 데이터 라이브러리**: React Query, SWR 등으로 **캐싱, 중복 제거, 재시도, 동기화**를 표준화한다.
> - **Suspense 기반 패턴**: 리소스 래퍼/서버 컴포넌트로 **로딩/오류 처리**를 선언적으로 구성한다.

### 분석 보내기

- 화면 노출/전환 시점에 **빈 의존성 배열 `[]`** Effect로 페이지뷰/노출 로그를 전송한다. 비동기 실패에 대비해 재시도 정책이나 배칭을 고려한다.

```tsx
useEffect(() => {
  analytics.pageView(location.pathname);
}, []);
```

### Effect가 아닌 경우: 애플리케이션 초기화

- 전역 단 한 번의 설정(예: 라이브러리 초기화, 설정 주입)은 **모듈 스코프** 또는 앱 부트스트랩 코드에서 수행한다. 컴포넌트 Effect로 처리하면 **중복 초기화** 가능성이 있다.

```tsx
// app-setup.ts (모듈 스코프에서 1회 실행)
initSentry();
configureDayjs();
```

### Effect가 아닌 경우: 제품 구입하기

- 결제/구매/전송 등은 **사용자 이벤트**에 직접 묶어 처리해야 한다. Effect로 구매를 수행하면 렌더 재시도/복원으로 **중복 결제**가 발생할 수 있다.

```tsx
function BuyButton({ sku }: { sku: string }) {
  const onClick = async () => {
    await purchase(sku); // 이벤트 핸들러에서 실행
  };
  return <button onClick={onClick}>구매하기</button>;
}
```

## 위에서 설명한 모든 것들 적용해보기

- **원칙**: “구독/외부 인스턴스 설치는 Effect, 최신 로직은 `useEvent` 또는 ref”. 의존성은 **정확하게**, 정리는 **대칭적으로** 구현한다.

```tsx
function ChatRoom({ roomId, onMessage }: { roomId: string; onMessage: (m: string) => void }) {
  const onMsg = useEvent(onMessage); // 최신 핸들러
  useEffect(() => {
    const conn = createConnection(roomId);
    conn.connect();
    const handler = (m: string) => onMsg(m);
    conn.on('message', handler);
    return () => {
      conn.off('message', handler);
      conn.disconnect();
    };
  }, [roomId]); // 최소 의존성
  return null;
}
```

> **각각의 렌더링은 각각의 고유한 Effect를 갖습니다.**  
> 의존성에 따라 새로운 클로저가 캡처되며, React는 **다음 Effect를 실행하기 전에 이전 Effect를 정리**한다. 의존성이 바뀌면 이전 구독/타이머를 안전하게 해제하고 새 구독/타이머를 설치한다.

## 요약

- 이벤트와 달리 Effect는 **렌더링에 의해** 발생한다.
- Effect는 컴포넌트를 **외부 시스템과 동기화**하는 데 사용한다.
- 기본적으로 Effect는 **모든 렌더링 후**(초기 렌더 포함) 실행된다.
- React는 의존성이 지난 렌더와 같으면 **Effect를 건너뛴다**.
- 의존성은 **코드가 결정**하며 “선택”할 수 없다.
- 빈 의존성 배열 `[]`은 **마운트 시 한 번**을 의미한다.
- **Strict Mode(개발)** 에서는 컴포넌트가 **두 번 마운트**되어 정리 로직의 견고함을 점검한다.
- React는 다음 Effect 실행 전과 언마운트 시 **정리 함수를 호출**한다.

## 인사이트

- Effect는 **동기화용 탈출구**로 최소화하고, 계산/표시/이벤트 중심 로직은 각각 **렌더/상태/핸들러**로 분리한다.
- 의존성은 “자동으로 결정된다”는 점을 기억하고, ESLint 규칙으로 **누락을 방지**한다.
- **`useEvent`(또는 ref 동기화)** 를 사용해 재구독 없이 최신 로직을 실행하고, 의존성은 **최소화**한다.
- 데이터는 **서버 로딩/전용 라이브러리/Suspense**를 우선하여, Effect 기반 페칭의 수동 관리 비용을 줄인다.
- Strict Mode의 **이중 실행**은 버그를 드러내기 위한 설계이므로, **정리와 대칭성**을 지키는 방향으로 대응한다.
