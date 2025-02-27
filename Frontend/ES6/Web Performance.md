# 🏎️ 웹 성능 최적화: requestAnimationFrame & console.time()

## 📌 개요

웹 성능 최적화에서 중요한 두 가지 개념인 `requestAnimationFrame`과 `console.time()`에 대해 알아본다.
`requestAnimationFrame`은 브라우저의 렌더링 주기에 맞춰 최적화된 애니메이션을 구현할 수 있도록 도와주며,
`console.time()`은 코드 실행 시간을 측정하여 성능을 개선하는 데 활용된다.

---

## 🚀 requestAnimationFrame 개념 및 활용

### 🔍 기존 방식: `setTimeout`, `setInterval`

웹 애니메이션을 구현할 때 가장 많이 사용되는 방법 중 하나는 `setTimeout` 또는 `setInterval`을 활용하는 것이다. 하지만 이 방법들은 브라우저의 리페인트(화면 갱신) 주기와 별개로 실행되므로 **성능 저하 및 화면 끊김(FPS 저하)** 등의 문제가 발생할 수 있다.

```js
function animate() {
  setTimeout(() => {
    // 애니메이션 로직 실행
    console.log('Animating...');
    animate();
  }, 16); // 60FPS를 목표로 16ms마다 실행
}

animate();
```

### ✅ 해결책: `requestAnimationFrame`

`requestAnimationFrame`은 브라우저의 화면 갱신 주기(보통 16.67ms, 60FPS)에 맞춰 애니메이션을 실행할 수 있도록 제공되는 API이다.
이를 활용하면 **성능을 최적화하고 화면 끊김 현상을 줄일 수 있다.**

```js
function animate() {
  requestAnimationFrame(animate); // 다음 프레임 요청
  console.log('Animating...');
}

animate();
```

📌 **장점**

- 브라우저의 리페인트 주기에 맞춰 실행되므로 CPU/GPU 성능을 효율적으로 사용 가능
- 비활성 탭에서는 실행이 자동으로 중단되어 불필요한 리소스 낭비 방지
- 애니메이션 성능이 향상되어 부드러운 사용자 경험 제공

---

## 🎯 console.time()을 활용한 성능 측정 및 디버깅

### 🔍 기존 문제: 성능 측정이 어려움

웹 애플리케이션을 개발할 때 **특정 코드가 실행되는 데 걸리는 시간**을 측정하는 것은 성능 최적화의 핵심이다.
기존에는 `Date.now()`를 활용하여 실행 시간을 측정했지만, 이는 직관적이지 않고 여러 구간을 측정하는 데 비효율적이다.

```js
const start = Date.now();
// 실행할 코드
for (let i = 0; i < 1000000; i++) {}
const end = Date.now();
console.log(`Execution Time: ${end - start}ms`);
```

### ✅ 해결책: `console.time()` & `console.timeEnd()`

`console.time()`을 사용하면 **코드 실행 시간을 정확하게 측정**할 수 있으며, `console.timeEnd()`를 호출하면 결과를 출력할 수 있다.

```js
console.time('Loop Execution Time');
for (let i = 0; i < 1000000; i++) {}
console.timeEnd('Loop Execution Time');
```

📌 **장점**

- 실행 시간을 직관적으로 확인 가능
- 특정 구간별 성능 비교 및 병목 현상 파악에 용이
- 개발 도구(F12 개발자 도구)와 함께 활용 시 더욱 강력한 성능 분석 가능

---

## 💡 실제 적용 경험

### 🏗️ FPS 유지 및 렌더링 최적화

프로젝트에서 애니메이션 성능 개선이 필요할 때 `requestAnimationFrame`을 활용하여 **FPS를 유지하며 부드러운 UI 렌더링을 구현**했다.

- 기존: `setTimeout`을 이용한 애니메이션 → **일정 간격이 지켜지지 않음 & 끊김 현상 발생**
- 개선: `requestAnimationFrame`으로 변경 → **부드럽고 최적화된 애니메이션 구현**

### 🎯 UI/UX 개선을 위한 성능 최적화 사례

- 특정 기능의 실행 시간이 길어지는 문제를 해결하기 위해 `console.time()`을 활용하여 성능을 분석하고 개선했다.
- 예를 들어, 리스트 렌더링 속도를 개선하기 위해 `console.time()`으로 각 단계별 성능을 측정하고 최적화할 부분을 식별할 수 있었다.

---

## 🔍 추가 학습할 내용

✔️ `requestAnimationFrame`과 `setTimeout`, `setInterval`의 동작 방식 비교 실험하기
✔️ 브라우저의 **페인트(Paint)** 및 **리플로우(Reflow)** 최적화 기법 학습
✔️ 개발자 도구에서 **FPS 측정 및 성능 프로파일링** 활용법 익히기
✔️ Web Workers와 함께 사용하여 성능을 더욱 향상하는 방법 탐구

---

## 📌 결론

웹 애니메이션과 성능 최적화는 사용자 경험(UX)에 직접적인 영향을 미친다. `requestAnimationFrame`을 활용하면 **부드러운 애니메이션**을 구현할 수 있으며, `console.time()`을 이용하면 **코드 실행 속도를 분석 및 개선**할 수 있다.
이를 활용하여 렌더링 최적화와 UI/UX 개선을 효과적으로 수행할 수 있다. 🚀
