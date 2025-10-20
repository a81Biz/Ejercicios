# .NET Full-Stack Interview Study Guide

*(Formal Technical Edition — Complete)*

---

## 1  JavaScript Fundamentals (React / Angular)

### 1.1 Closures — Definition and Mechanics

A **closure** is the combination of a function and its surrounding lexical environment.
It allows an inner function to access variables declared in its outer scope even after the outer function has finished executing.

#### Code Example

```javascript
function counter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const increment = counter();
console.log(increment()); // 1
console.log(increment()); // 2
```

#### Explanation

* The variable `count` lives inside the lexical environment of `counter`.
* When `counter()` returns the inner anonymous function, that function “remembers” `count`.
* Every invocation of `increment()` accesses the same `count` variable, demonstrating **persistent state through closure**.

#### Senior Insight

Closures underpin many advanced JavaScript patterns—such as data hiding, module patterns, and custom hooks in React.
They are also critical for asynchronous logic where state must persist across event-loop ticks.

---

### 1.2 Prototype and Inheritance Model

JavaScript implements **prototypal inheritance**, not classical inheritance.
Every object has an internal link (`[[Prototype]]`) pointing to another object, known as its prototype.

#### Code Example

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function() {
  console.log(`Hello, my name is ${this.name}`);
};

const john = new Person("John");
john.sayHello(); // Hello, my name is John
```

#### Explanation

* When `john.sayHello()` is called, the engine looks for `sayHello` on `john`.
* Not found → lookup proceeds to `Person.prototype`.
* This **prototype chain** continues until the base object (`Object.prototype`) or `null`.

#### Senior Insight

Understanding prototype chains is fundamental to debugging inheritance issues and to writing memory-efficient objects—methods defined on `prototype` are shared among all instances, reducing RAM footprint.

---

### 1.3 Event Loop and Concurrency Model

The **Event Loop** manages execution of JavaScript’s single-threaded runtime, coordinating the **Call Stack**, **Microtask Queue**, and **Callback Queue**.

#### Execution Phases

1. **Call Stack** — synchronous functions execute here.
2. **Web APIs / Task Sources** — asynchronous operations (timers, fetch, DOM events) register callbacks.
3. **Queues** — once complete, callbacks enter microtask (queue for Promises) or macrotask (queue for timers, IO).
4. **Event Loop** — continuously checks whether the stack is empty and pushes queued tasks.

#### Example

```javascript
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
console.log("D");
```

**Output:** A → D → C → B

#### Senior Insight

Promises and `async/await` operate through the microtask queue, which executes **before** macrotasks.
This ordering can cause subtle timing issues in high-throughput front-end code or Node.js APIs.

---

### 1.4 State Management in React and Angular

#### React State Management

* **Local State** — handled via `useState()` or `this.state` in class components.
* **Derived State** — computed from props or context.
* **Global State** — often managed with libraries such as Redux, MobX, or Zustand.

##### Redux Example

```javascript
// action
const increment = () => ({ type: "INCREMENT" });

// reducer
function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case "INCREMENT":
      return { value: state.value + 1 };
    default:
      return state;
  }
}
```

#### Angular State Management

* **Services + RxJS Observables** act as singletons for shared state.
* **NgRx** applies Redux-style principles using reactive streams.

##### Angular Service Example

```typescript
@Injectable({ providedIn: 'root' })
export class CounterService {
  private count = new BehaviorSubject<number>(0);
  count$ = this.count.asObservable();

  increment() {
    this.count.next(this.count.value + 1);
  }
}
```

#### Senior Insight

React emphasizes **immutable updates** and unidirectional data flow, whereas Angular promotes **reactive streams** via RxJS.
Both require awareness of **change detection** and **performance implications** when state mutates frequently.

---

### 1.5 Observables and Reactive Programming

An **Observable** represents a stream of asynchronous data that can be observed over time.

#### Concept

* **Observer** subscribes to the stream.
* **Observable** emits values via `next()`, `error()`, and `complete()`.
* **Operators** (e.g., `map`, `filter`, `mergeMap`) transform streams declaratively.

#### Example

```typescript
import { from } from 'rxjs';
import { map } from 'rxjs/operators';

from([1, 2, 3])
  .pipe(map(x => x * 2))
  .subscribe(value => console.log(value)); // 2 4 6
```

#### Senior Insight

Reactive programming enables **push-based data flows** ideal for UI events, WebSocket feeds, and streaming APIs.
Mastery of RxJS operators is a distinguishing skill in senior Angular roles.

---

### 1.6 Summary of Frontend Best Practices

| Aspect        | Key Guideline                                                        |
| :------------ | :------------------------------------------------------------------- |
| Performance   | Use memoization (`React.memo`, `useMemo`) and lazy loading.          |
| Security      | Sanitize inputs to avoid XSS; use HTTPS and Content Security Policy. |
| Accessibility | Provide ARIA labels and keyboard navigation.                         |
| Testing       | Adopt Jest / React Testing Library / Karma for unit tests.           |
| Deployment    | Bundle and minify; use CDNs and cache strategies.                    |
