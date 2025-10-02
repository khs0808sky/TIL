# TIL

## 📅 목차

- [2025-10-01](#2025-10-01)
- [2025-10-02](#2025-10-02)

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

### 2025-10-02

---

## React 한눈에 보기

**React**는 UI를 **컴포넌트** 단위로 쪼개고, **상태(state)** 가 바뀌면 필요한 부분만 다시 렌더링하는 라이브러리야.

### 핵심 개념 요약

* **컴포넌트**: 화면 조각. 함수형 컴포넌트가 표준.
* **Props**: 부모 → 자식으로 내려주는 읽기 전용 데이터.
* **State**: 컴포넌트 내부에서 관리하는 변경 가능한 데이터.
* **렌더링**: `state/props`가 바뀌면 함수가 다시 실행되어 UI가 새로 계산됨.
* **단방향 데이터 흐름**: 데이터는 위에서 아래로 흐름(부모→자식). 상위로 올리고 싶으면 **state 끌어올리기(lifting)**.

---

## useState 완전 정복

`useState`는 함수형 컴포넌트에서 **상태**를 만들고 업데이트하는 기본 훅이야.

### 1) 기본 사용

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0); // [현재값, setter]

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

* `useState(초기값)` → `[state, setState]`
* `setState(새값)` 호출 시 렌더 트리거

---

### 2) **배칭(Batching)** 과 비동기 업데이트

React 18부터는 **이벤트 루프 내의 여러 setState가 한 번에 배치**되어 렌더가 최소화돼.

```jsx
// 한 번만 렌더링
setCount(count + 1);
setCount(count + 1); // 같은 렌더 사이클에서 count는 여전히 이전 값
```

연속 업데이트 시에는 **업데이트 함수 형태**(functional update)를 쓰자:

```jsx
setCount(c => c + 1);
setCount(c => c + 1); // 최종 +2
```

---

### 3) **업데이트 함수(Functional Update)** 가 필요한 순간

* 이전 상태값을 기반으로 다음 상태를 계산할 때 **항상** 함수형을 써라.
* 비동기/지연 로직, 이벤트 핸들러가 복잡할수록 안전하다.

```jsx
setItems(prev => [...prev, newItem]);
setOpen(prev => !prev);
```

---

### 4) **지연 초기화(Lazy Init)** — 무거운 초기값 계산 최적화

초기값 계산이 비싸면 콜백으로 전달:

```jsx
const [data, setData] = useState(() => expensiveCompute()); // 최초 렌더 1회만 실행
```

---

### 5) **객체/배열 업데이트: 불변성 유지**

React는 얕은 비교로 변경 여부를 판단하므로 **새 참조**를 만들어야 해.

```jsx
// 객체
setUser(u => ({ ...u, name: "철수" }));

// 배열
setTodos(ts => ts.map(t => t.id === id ? { ...t, done: !t.done } : t));
setTodos(ts => ts.filter(t => t.id !== id));
setTodos(ts => [...ts, { id: Date.now(), text }]);
```

---

### 6) **파생 상태(Derived State) 피하기**

props나 state로 **계산 가능한 값**은 굳이 또 state로 들고 있지 말자.

```jsx
// ❌ 파생 상태를 또 들고 있지 말고
const discounted = price * 0.9; // ✅ 렌더 중 계산
```

---

### 7) **stale 클로저(오래된 값 캡처)** 주의

비동기 콜백에서 **오래된 state**를 캡처할 수 있어. 함수형 업데이트로 방지.

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(c => c + 1); // 최신 c 사용
  }, 1000);
  return () => clearInterval(id);
}, []);
```

---

### 8) **상태 올리기(Lifting State Up)** 와 상태 소유권

여러 컴포넌트가 동일한 상태를 필요로 하면 **가까운 공통 부모**로 올려라.

```jsx
function Parent(){
  const [value, setValue] = useState("");
  return (
    <>
      <Input value={value} onChange={setValue} />
      <Preview value={value} />
    </>
  );
}
```

---

### 9) **useState vs useRef vs useReducer**

* `useState`: 렌더에 영향을 주는 값. 호출 시 렌더 트리거.
* `useRef`: **변하지만 렌더를 유발하지 않는 값**(타이머 id, DOM 참조, 캐시).
* `useReducer`: 업데이트 로직이 복잡/여러 액션으로 나뉠 때.

```jsx
function reducer(state, action){
  switch(action.type){
    case "inc": return { ...state, count: state.count + 1 };
    case "reset": return { ...state, count: 0 };
    default: return state;
  }
}
```

---

### 10) 입력 폼과 **제어 컴포넌트**

`value`와 `onChange`를 통해 입력값을 **state로 통제**.

```jsx
function Form() {
  const [name, setName] = useState("");
  const onChange = e => setName(e.target.value);
  return <input value={name} onChange={onChange} placeholder="이름" />;
}
```

---

### 11) **성능 팁**

* 연속 setState는 **함수형 업데이트**로 합치기.
* 무거운 계산은 **지연 초기화** + `useMemo`.
* 큰 리스트는 **가상 스크롤**(react-window 등).
* 이벤트 과다 호출은 **디바운스/스로틀** 적용.

---

### 12) **자주 하는 실수 정리**

1. `setState(state + 1)`를 여러 번 연속 호출 → 예상보다 적게 증가
   → **업데이트 함수 형태** 사용.
2. 객체/배열을 직접 수정 후 `setState(sameRef)`
   → 참조가 그대로라 렌더 안 됨. **새 객체/배열 생성**.
3. 파생 가능한 값을 또 state로 듦
   → 버그·동기화 이슈. **렌더 중 계산**으로 대체.
4. 비동기 콜백에서 오래된 state 사용
   → 함수형 업데이트/의존성 배열 점검.

---

### 13) 미니 실습(짧게)

**(1) +2 버튼 (배칭/함수형 업데이트)**

```jsx
function PlusTwo(){
  const [n, setN] = useState(0);
  const inc2 = () => {
    setN(v => v + 1);
    setN(v => v + 1);
  };
  return <button onClick={inc2}>{n}</button>;
}
```

**(2) Todo 토글/삭제 (불변성)**

```jsx
function Todos(){
  const [todos, setTodos] = useState([{id:1, text:"JS"}, {id:2, text:"React"}]);

  const toggle = id =>
    setTodos(ts => ts.map(t => t.id===id ? {...t, done:!t.done} : t));

  const remove = id =>
    setTodos(ts => ts.filter(t => t.id!==id));

  return (
    <ul>
      {todos.map(t => (
        <li key={t.id} onClick={() => toggle(t.id)}>
          {t.text}{t.done ? " ✅" : ""}
          <button onClick={(e)=>{e.stopPropagation(); remove(t.id);}}>삭제</button>
        </li>
      ))}
    </ul>
  );
}
```

---

### 14) 기억할 한 문장

> **useState는 “상태값 + setter”를 제공한다.**
> 이전 값을 기반으로 바꿀 땐 **항상 함수형 업데이트**,
> 객체/배열은 **불변성 유지**, 무거운 초기화는 **지연 초기화**로!

---

📅[목차로 돌아가기](#-목차)

---

