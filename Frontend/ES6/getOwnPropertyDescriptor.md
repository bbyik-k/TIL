# Object.getOwnPropertyDescriptor(s)는 왜 존재하는가?

객체를 “복사한다”는 말은 생각보다 단순하지 않다.  
값만 복사하면 끝나는 경우도 있지만, 자바스크립트 객체의 프로퍼티는 값 이상의 정보를 가지고 있기 때문이다.

### 프로퍼티에는 값 말고도 정보가 있다

자바스크립트에서 하나의 프로퍼티는 단순히 key: value 구조가 아니다.  
엔진 내부적으로는 다음과 같은 프로퍼티 디스크립터로 관리된다.

- `value` / `get` / `set`
- `writable`
- `enumerable`
- `configurable`

즉, 어떤 값이 얼마나 수정 가능한지, 열거 가능한지, 접근 방식이 함수인지까지 포함된 “설계도”가 프로퍼티다.

### 기존 복사 방식의 한계

`Object.assign`, `spread` 문법의 문제

```js
const copy = { ...obj };
```

이 방식은 편리하지만 중요한 한계가 있다.

- enumerable한 own 프로퍼티만 복사
- writable / configurable 정보는 무시
- getter / setter는 실행되어 “값”으로 붕괴됨
- 접근자 프로퍼티의 구조는 보존되지 않음
- 즉, 객체의 겉모습만 복사할 뿐, 구조는 복사하지 않는다.

## getOwnPropertyDescriptor란?

```js
Object.getOwnPropertyDescriptor(obj, 'prop');
```

- 객체가 직접 소유한(own) 특정 프로퍼티 하나의 디스크립터(정의 정보) 를 반환하는 메서드다.

반환값은 “값”이 아니라 다음과 같은 메타 정보다.

- value 또는 get/set
- writable / enumerable / configurable

이 메서드는 _이 프로퍼티가 어떻게 정의되어 있는가_ 를 확인하기 위한 도구다.

## getOwnPropertyDescriptors 란??

```js
Object.getOwnPropertyDescriptors(obj);
```

ES2017에서 추가된 메서드로, _객체가 직접 가진 모든 own 프로퍼티의 디스크립터를 한 번에_ 객체 형태로 반환한다.  
이 메서드는 사실상 정확한 객체 복사를 가능하게 만든 핵심 도구다.

## 언제 사용하나

> getter / setter를 보존한 객체 복사

```js
const clone = Object.defineProperties(
  //
  {},
  Object.getOwnPropertyDescriptors(original),
);
```

- getter를 실행하지 않고
- getter 자체를 복사하고
- writable / enumerable / configurable 설정까지 그대로 유지

> 객체의 “행동 방식”까지 복사

## 제한 사항

- 프로토타입 체인에 있는 프로퍼티는 복사하지 않는다
- 오직 own property만 대상이다
  이 점 덕분에 의도치 않은 상속 프로퍼티 복사 문제를 피할 수 있다.

## 참고

- [JAVASCRIPT.INFO 프로퍼티 플래그와 설명자](https://ko.javascript.info/property-descriptors)
- [MDN Object.prototype.hasOwnProperty()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)
