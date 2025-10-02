# TIL

## 📅 목차

- [2025-10-01](#2025-10-01)

<br><br><br>

---

### 2025-10-01

---

### 자바스크립트(JavaScript)란?

웹 브라우저와 Node.js에서 동작하는 **동적 타입**의 **멀티패러다임** 언어.
함수형·객체지향·명령형 스타일을 모두 지원하며, **이벤트 루프** 기반의 **싱글 스레드 비동기** 모델을 사용.

---

## 1) 기본 문법과 타입

### 변수

```js
let a = 1;      // 재할당 O, 블록 스코프
const b = 2;    // 재할당 X, 블록 스코프
var c = 3;      // 함수 스코프(지양)
```

### 원시 타입(Primitive)

`number`, `string`, `boolean`, `null`, `undefined`, `bigint`, `symbol`
참조 타입은 `object` (Array, Function, Date 등 포함)

### 비교(== vs ===)

* `==` 는 **암묵적 형 변환** 후 비교 → 예측 어려움
* `===` 는 타입까지 엄격 비교 → **권장**

```js
0 == '0'   // true
0 === '0'  // false
```

### Truthy/Falsy

Falsy: `0, "", null, undefined, NaN, false`
그 외 대부분은 truthy

---

## 2) 스코프/호이스팅/클로저

### 스코프(Scope)

* **함수 스코프**: `var`
* **블록 스코프**: `let`, `const`

### 호이스팅(Hoisting)

* 선언이 스코프의 최상단으로 끌어올려진 것처럼 동작
* `let/const`는 **TDZ(Temporal Dead Zone)** 로 초기화 전 접근 시 에러

### 클로저(Closure)

내부 함수가 **외부 함수의 변수**에 접근하는 메커니즘.

```js
function counter() {
  let n = 0;
  return () => ++n;
}
const inc = counter();
inc(); // 1
inc(); // 2
```

---

## 3) this 바인딩

`this`는 **호출 방식**에 따라 달라짐.

* **일반 함수 호출**: 브라우저 전역은 `window`, strict 모드에선 `undefined`
* **메서드 호출**: 점 앞의 객체
* **생성자(new)**: 새로 만든 인스턴스
* **call/apply/bind**: 수동 바인딩
* **화살표 함수**: **자신만의 this가 없음**, 상위 스코프의 this를 캡처

```js
const obj = {
  x: 10,
  f() { console.log(this.x); }
};
obj.f();            // 10
const g = obj.f;
g();                // undefined (strict) 또는 window.x
```

---

## 4) 프로토타입과 클래스

### 프로토타입

모든 객체는 내부 슬롯 `[[Prototype]]`을 통해 상위 객체(프로토타입)와 연결.

```js
function Person(name){ this.name = name; }
Person.prototype.say = function(){ return `Hi ${this.name}`; };

const p = new Person('철수');
p.say(); // "Hi 철수"
```

### 클래스 문법(ES6)

프로토타입의 문법 설탕(sugar).

```js
class Person {
  constructor(name){ this.name = name; }
  say(){ return `Hi ${this.name}`; }
  static of(n){ return new Person(n); }
}
```

---

## 5) 비동기: 이벤트 루프, 콜백, 프로미스, async/await

### 이벤트 루프 요약

* **콜 스택**이 비면 **태스크 큐(매크로/마이크로)** 의 작업을 스택으로 푸시
* 마이크로태스크(Promise then)가 매크로태스크(setTimeout)보다 **우선**

### 콜백 → 프로미스 → async/await

```js
// Promise
fetch('/api')
  .then(r => r.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));

// async/await
async function load() {
  try {
    const r = await fetch('/api');
    const data = await r.json();
    console.log(data);
  } catch (e) {
    console.error(e);
  }
}
```

---

## 6) 모듈(Modules)

### ES 모듈(브라우저/Node)

```js
// add.js
export function add(a,b){ return a+b; }

// main.js
import { add } from './add.js';
```

### CommonJS(Node 전통)

```js
// add.cjs
exports.add = (a,b) => a+b;

// main.cjs
const { add } = require('./add.cjs');
```

---

## 7) DOM & 이벤트(브라우저)

```html
<button id="btn">클릭</button>
<script>
  const btn = document.getElementById('btn');
  btn.addEventListener('click', () => {
    alert('clicked');
  }, { once: true }); // 한 번만
</script>
```

**이벤트 위임**: 다수의 자식 대신 상위에서 한 번에 처리

```js
document.body.addEventListener('click', (e)=>{
  if(e.target.matches('.item')) { /* ... */ }
});
```

---

## 8) 네트워킹: Fetch API

```js
// GET
const res = await fetch('/users?limit=10');
const users = await res.json();

// POST(JSON)
await fetch('/users', {
  method: 'POST',
  headers: { 'Content-Type':'application/json' },
  body: JSON.stringify({ name:'영희' })
});
```

에러 처리 팁:

```js
async function safeFetch(url, opt) {
  const r = await fetch(url, opt);
  if(!r.ok) throw new Error(`HTTP ${r.status}`);
  return r.json();
}
```

---

## 9) 타입 안정성 옵션

* **JSDoc**: 주석으로 타입 힌트 제공
* **TypeScript**: JS 상위 집합, 컴파일 단계에서 타입 체크 → 대규모 프로젝트 권장

```ts
// TypeScript 예시
function add(a: number, b: number): number {
  return a + b;
}
```

---

## 10) 배열/객체 유틸 패턴

```js
// 불변 업데이트
const arr = [1,2,3];
const arr2 = [...arr, 4];

const user = { id:1, name:'철수' };
const user2 = { ...user, name:'영희' };

// 고차 함수
[1,2,3].map(x => x*2).filter(x => x>2).reduce((a,b)=>a+b, 0);
```

---

## 11) 에러 처리 & 예외

```js
try {
  mightThrow();
} catch (e) {
  console.error(e);
} finally {
  cleanup();
}
```

비동기 함수 내에서는 `try/catch` + `await` 조합을 사용.

---

## 12) 성능·최적화 포인트

* **디바운스/스로틀**: 스크롤/입력 이벤트 과다 호출 제어
* **메모이제이션**: 동일 입력의 반복 연산 캐싱
* **웹팩/Vite** 번들 최적화, 코드 스플리팅, 트리 셰이킹
* **Web Worker**: 무거운 연산을 워커 스레드로 분리

```js
// 디바운스 예시
function debounce(fn, delay){
  let t;
  return (...args)=>{
    clearTimeout(t);
    t = setTimeout(()=>fn(...args), delay);
  };
}
```

---

## 13) 테스트 기초

* 단위 테스트: **Jest / Vitest**
* E2E: **Playwright / Cypress**
* 핵심: **순수 함수**로 분리 → 테스트 용이성↑

```js
// 예시 (Jest)
test('add', () => {
  expect(add(1,2)).toBe(3);
});
```

---

## 14) 실행 환경 요약

* **브라우저 런타임**: DOM, BOM, Web APIs 제공
* **Node.js 런타임**: 파일/네트워크/프로세스 API, CommonJS/ESM 지원
* **Deno/Bun**: 대안 런타임 (권한 모델·속도·번들링 등 강점)

---

## 15) 실전 설계 팁

* 상태는 **단방향 데이터 흐름**을 기본으로(예: React)
* 부작용(IO)은 **경계(서비스/리포지토리 레이어)** 로 모으기
* 공통 유틸/타입/상수는 별도 모듈로 분리
* API 계층: **fetch 래퍼 + 에러 규약** 통일
* 비동기 로직: **async/await** 일관 사용, 에러 공통 처리기 마련

---

📅[목차로 돌아가기](#-목차)

---

