# JavaScript 복습

- 호이스팅  
  - JavaScript의 고유한 동작으로, 변수 선언, 함수 선언이 해당 코드의 실행 전에 최상위로 끌어올려지는 것처럼 동작하는 개념  
  - TDZ (Temporal Dead Zone): 일시적 사각지대, let / const 등 선언 전 사용  
- Array.from  
  - Array.from(  
  -  {length: 20},  
  -  () \=\> Array(10).fill(0)  
  - );  
  - 예전엔 // Array.prototype.slice 방식  
    - const arr1 \= Array.prototype.slice.call(arrayLike);  
  - Array.from 에선 유사 배열 객체로 length key 를 통해 길이 지정  
  


- 프로토 (proto)  
- Set  
- Map

- ‘A’ && ‘B’ && ‘C’ // C \-\> 참이면 다음으로 넘어감  
- 0 || 1 //1 \-\> 거짓이면 다음으로 넘어감  
- \!\! 이중 부정 연산자 (Double Negation)  
  - 값을 Boolean 타입으로 변환하는 방법  
1. 첫 번째 \!(부정 연산자)  
- 값을 Boolean으로 변환한 뒤, 해당 값을 부정(true \-\> false, false \-\> true)합니다.  
2. 두 번째 \!  
- 다시 한 번 부정하여 원래의 논리 값을 반환합니다.  
- 결과적으로, \!\!는 값을 정확히 true 또는 false로 변환하는 역할을 합니다.	

- Nullish 병합 (??)  
  - n ?? 7 \- 0 도 사용 가능  
  - null, undefined 외엔 출력  
  - falsy 값 출력

- 삼항 연산자  
  - ? :  
-


- 객체지향 / OOP  
  - 

## 25\. 01\. 24

### 비동기 처리 / 콜백 / Callback

* synchronous (동기) vs Asynchronous (비동기)  
  * JS: synchronous \- 동기적  
    *  호이스팅 이후 코드가 순서대로 실행  
* ()=\>{} Vs Func()  
  * `setTimeout(() => writeLog(2), 1000);`의 경우, 화살표 함수를 사용해 `setTimeout`에 함수 참조를 전달합니다. 1초 후에 `setTimeout`이 화살표 함수를 실행하며, 이 과정에서 `writeLog(2)`가 호출되고 정상적으로 `2`가 출력됩니다.  
  * 반면, `setTimeout(writeLog(2), 1000);`는 `writeLog(2)`를 즉시 실행한 후, \*\*반환값(`undefined`)\*\*을 `setTimeout`의 첫 번째 인자로 전달합니다. `setTimeout`은 첫 번째 인자로 **함수 참조**를 기대하기 때문에, 올바른 함수가 전달되지 않아 에러가 발생합니다.  
  * 면접에서 주의할 포인트  
    * writeLog(2)의 반환값은 2가 아닌 undefined라는 점을 명확히 언급해야 합니다.  
    * setTimeout이 callback 함수 참조를 필요로 한다는 점을 강조하세요.  
    * 화살표 함수는 callback으로 함수를 전달할 때 유용한 방법이라는 점도 언급하면 가산점을 받을 수 있습니다.

### promise

* Promise is a JavaScirpt object for asynchronous operation.  
* State\!  
  * pending \-\> fulfilled or rejected  
* producer / consumer (제공자 / 소비자) 견해 이해  
  * producer: promise  
  * promise: class  
* Chaining  
  * .then().catch()  
  * then 에서 다시 promise 를 반환 후 catch 작업

### async / await