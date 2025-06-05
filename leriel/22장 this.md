---

## 📌 22.1 this 키워드

### this란?

- **this**는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수(self-referencing variable)
- 함수를 호출할 때 함수가 어떻게 호출되었는지에 따라 동적으로 결정됨
- 객체지향 프로그래밍에서 메서드가 자신이 속한 객체의 프로퍼티를 참조하기 위해 필요

### this의 특징

- 자바스크립트 엔진에 의해 암묵적으로 생성됨
- 코드 어디서든 참조 가능
- 전역에서 this는 전역 객체(브라우저: window, Node.js: global) 참조
- this 값은 **함수 호출 방식에 의해 동적으로 결정**됨

```jsx
// 전역에서의 this
console.log(this); // 브라우저: window, Node.js: global

// 함수 내부에서의 this
function square(number) {
  console.log(this); // window
  return number * number;
}
square(2);

// 메서드 내부에서의 this
const person = {
  name: '홍길동',
  getName() {
    console.log(this); // {name: '홍길동', getName: ƒ}
    return this.name;
  }
};
console.log(person.getName()); // 홍길동

```

---

## 📌 22.2 함수 호출 방식과 this 바인딩

자바스크립트에서 this 바인딩은 함수가 어떻게 호출되는지에 따라 동적으로 결정됩니다.

### 함수 호출 방식에 따른 this 바인딩 결정

1. 일반 함수 호출 - 전역객체
2. 메서드 호출  - 메서드를 호출한 객체
3. 생성자 함수 호출 - 생성자 함수가 생성할 인스턴스
4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

---

## 📌 22.2.1 일반 함수 호출

### 특징

- 일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 **전역 객체가 바인딩**됨
- strict mode에서는 undefined가 바인딩됨

```jsx
function foo() {
  console.log("foo's this:", this); // window

  function bar() {
    console.log("bar's this:", this); // window
  }
  bar();
}
foo();

```

### 🚨 일반 함수 내부에서의 this 문제점

- 메서드 내의 중첩 함수나 콜백 함수의 this가 전역 객체를 가리키는 문제 발생
- 외부 함수인 메서드의 this와 내부 함수의 this가 서로 다른 객체를 가리킴

### 🔧 해결 방법

1. **this 바인딩을 변수에 할당**

```jsx
const obj = {
  value: 100,
  method() {
    // this를 that에 저장
    const that = this;

    // 콜백 함수 내부에서 that을 참조
    setTimeout(function() {
      console.log(that.value); // 100
    }, 100);
  }
};

```

1. **화살표 함수 사용**

```jsx
const obj = {
  value: 100,
  method() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킴
    setTimeout(() => {
      console.log(this.value); // 100
    }, 100);
  }
};

```

1. **Function.prototype.bind() 사용**

```jsx
const obj = {
  value: 100,
  method() {
    // bind 메서드로 this 바인딩
    setTimeout(function() {
      console.log(this.value); // 100
    }.bind(this), 100);
  }
};

```

---

## 📌 22.2.2 메서드 호출

### 특징

- 메서드 내부의 this는 메서드를 **호출한 객체**에 바인딩됨
- 메서드를 소유한 객체가 아닌, 메서드를 호출한 객체에 바인딩되는 점이 중요

```jsx
const person = {
  name: '홍길동',
  getName() {
    return this.name;
  }
};

console.log(person.getName()); // 홍길동

const anotherPerson = {
  name: '김철수'
};

// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// 메서드를 호출한 객체인 anotherPerson에 this가 바인딩됨
console.log(anotherPerson.getName()); // 김철수

// 메서드를 변수에 할당하면 일반 함수로 호출됨
const getName = person.getName;
console.log(getName()); // '' (window.name은 브라우저 환경에서 빈 문자열)

```

### 프로토타입 메서드 내부의 this

- 프로토타입 메서드 내부의 this도 해당 메서드를 호출한 객체에 바인딩됨

```jsx
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function() {
  return this.name;
};

const me = new Person('홍길동');
console.log(me.getName()); // 홍길동

Person.prototype.name = '김철수';
console.log(Person.prototype.getName()); // 김철수

```

---

## 📌 22.2.3 생성자 함수 호출

### 특징

- 생성자 함수 내부의 this는 생성자 함수가 **생성할 인스턴스**에 바인딩됨
- new 연산자 없이 호출하면 일반 함수로 동작함

```jsx
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성할 인스턴스를 가리킴
  this.radius = radius;
  this.getDiameter = function() {
    return 2 * this.radius;
  };
}

// 생성자 함수로 호출
const circle1 = new Circle(5);
console.log(circle1.getDiameter()); // 10

// 일반 함수로 호출
const circle2 = Circle(10);
console.log(circle2); // undefined
// 일반 함수로 호출되어 this가 전역 객체에 바인딩됨
console.log(radius); // 10 (window.radius)

```

---

## 📌 22.2.4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출

### apply와 call 메서드

- 함수를 호출하면서 첫 번째 인수로 전달한 객체에 this를 바인딩
- 두 메서드의 본질적 기능은 함수 호출
- apply: 함수 인수를 배열로 전달
- call: 함수 인수를 쉼표로 구분한 리스트 형식으로 전달

```jsx
function getThisBinding() {
  console.log(arguments);
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// apply 메서드는 호출할 함수의 인수를 배열로 전달
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
getThisBinding().a  = 1 
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}

```

### bind 메서드

- 함수를 호출하지 않고 첫 번째 인수로 전달한 값으로 this 바인딩이 교체된 함수를 새롭게 생성하여 반환
- 메서드의 this와 내부 함수의 this가 불일치하는 문제를 해결하는데 유용

```jsx
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// bind 메서드는 함수를 호출하지 않고 this만 바인딩된 새로운 함수를 반환
const bindFn = getThisBinding.bind(thisArg);

console.log(bindFn()); // {a: 1}

```

### apply, call, bind 활용 예제

### 1. 유사 배열 객체를 배열로 변환

```jsx
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수 없이 호출하면 배열의 복사본을 생성
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3);
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// [1, 2, 3]

```

### 2. 생성자 함수 내부에서 다른 생성자 함수 호출

```jsx
function Person(name) {
  this.name = name;
}

function Student(name, subject) {
  // Student 생성자 함수에서 Person 생성자 함수 호출
  Person.call(this, name);
  this.subject = subject;
}

const student = new Student('홍길동', '수학');
console.log(student); // Student {name: '홍길동', subject: '수학'}

```

---

## 📝 요약

### this의 바인딩 결정 규칙

| **함수 호출 방식** | **this 바인딩** |
| --- | --- |
| 일반 함수 호출 | 전역 객체 (strict mode에서는 undefined) |
| 메서드 호출 | 메서드를 호출한 객체 |
| 생성자 함수 호출 | 생성자 함수가 생성할 인스턴스 |
| apply/call/bind 호출 | 메서드의 첫 번째 인수로 전달한 객체 |

### this 결정 시점

- 함수가 어떻게 호출되었는지에 따라 **런타임**에 동적으로 결정됨
- 화살표 함수는 예외: 상위 스코프의 this를 참조(lexical this)

---

## 일반 함수와 화살표 함수의 성능 차이

화살표 함수는 ES6에서 도입된 기능으로, 일반 함수와 비교하여 문법적 간결함 외에도 this 바인딩 동작 및 성능 측면에서 차이가 있습니다.

### 기본적인 차이점

| **특징** | **일반 함수** | **화살표 함수** |
| --- | --- | --- |
| this 바인딩 | 호출 방식에 따라 동적 결정 | 렉시컬 스코프의 this 사용 (상위 스코프) |
| arguments 객체 | 생성됨 | 생성되지 않음 |
| prototype 속성 | 있음 | 없음 |
| constructor | 생성자로 사용 가능 | 생성자로 사용 불가 |

### 성능적 차이

### 1. 인스턴스화 오버헤드

```jsx

javascript
// 일반 함수는 prototype과 constructor를 가짐
function RegularFunc() {}

// 화살표 함수는 prototype이 없음
const ArrowFunc = () => {};

console.log(RegularFunc.prototype);// {constructor: ƒ}
console.log(ArrowFunc.prototype);// undefined

```

- 일반 함수는 `prototype` 객체와 관련된 메모리 할당이 추가로 발생
- 화살표 함수는 `prototype` 객체가 없어 메모리 사용량이 적음

### 2. 바인딩 오버헤드

```jsx

javascript
const obj = {
  value: 42,

// 일반 함수는 호출될 때마다 this 바인딩이 결정됨
  regularMethod: function() {
    console.log(this.value);
  },

// 화살표 함수는 생성 시점에 this가 결정됨 (렉시컬 this)
  arrowMethod: () => {
    console.log(this.value);// 여기서 this는 상위 스코프의 this
  }
};

```

- 일반 함수는 호출될 때마다 this 바인딩을 결정하는 오버헤드 발생
- 화살표 함수는 생성될 때 한 번만 this를 결정하므로 반복 호출 시 더 효율적일 수 있음

### 3. arguments 객체 생성 비용

```jsx

javascript
// 일반 함수는 arguments 객체를 생성
function regular() {
  console.log(arguments);
}

// 화살표 함수는 arguments 객체를 생성하지 않음
const arrow = (...args) => {
  console.log(args);// 대신 rest 파라미터 사용
};

```

- 일반 함수는 arguments 객체 생성에 따른 오버헤드 발생
- 화살표 함수는 arguments 객체를 생성하지 않아 메모리 사용량 감소

### 4. 최적화 용이성

```jsx

javascript

// 일반 함수는 this 바인딩, arguments 등의 처리로 최적화가 복잡할 수 있음
function regularAdd(a, b) {
  return a + b;
}

// V8 엔진은 이런 패턴의 화살표 함수를 더 쉽게 최적화할 수 있음
const optimizedArrow = (a, b) => a + b;

```

- 화살표 함수는 단순한 구조로 JIT 컴파일러의 최적화가 용이함
- 특히 간단한 연산에서 화살표 함수가 더 최적화되기 쉬움

### 성능 벤치마크 결과

실제 성능 측정 결과, 대부분의 경우 화살표 함수와 일반 함수의 순수 실행 속도 차이는 미미합니다. 그러나 특정 상황에서는 차이가 있을 수 있습니다:

```jsx

javascript
// 벤치마크 예시 코드
const benchmark = (iterations) => {
// 일반 함수 테스트
  const regularStart = performance.now();
  for (let i = 0; i < iterations; i++) {
    (function(x) { return x * x; })(i);
  }
  const regularTime = performance.now() - regularStart;

// 화살표 함수 테스트
  const arrowStart = performance.now();
  for (let i = 0; i < iterations; i++) {
    ((x) => x * x)(i);
  }
  const arrowTime = performance.now() - arrowStart;

  console.log(`일반 함수: ${regularTime}ms`);
  console.log(`화살표 함수: ${arrowTime}ms`);
}

// 1000만 번 반복 실행
benchmark(10000000);

```

### 결론

1. **순수 성능만 고려한다면**:
    - 대부분의 일상적인 코드에서 두 함수 유형의 성능 차이는 무시할 만한 수준
    - 매우 높은 성능이 필요한 애플리케이션에서는 특정 사례별로 벤치마크 테스트를 수행하는 것이 좋음
2. **코드 스타일과 가독성을 고려한다면**:
    - 화살표 함수는 간결한 문법으로 코드 가독성을 높이고 유지보수성을 개선
    - this 바인딩 문제를 자연스럽게 해결하여 버그 발생 가능성 감소
3. **최적의 사용 시나리오**:
    - **화살표 함수 권장**: 콜백 함수, 메서드가 아닌 함수, 상위 스코프의 this를 사용해야 하는 경우
    - **일반 함수 권장**: 객체의 메서드, 생성자 함수, this를 동적으로 바인딩해야 하는 경우
4. **실제 성능 향상을 위한다면**:
    - 함수 유형보다 알고리즘 최적화가 성능에 더 큰 영향을 미침
    - 핫스팟(자주 실행되는 코드)을 식별하고 해당 부분을 최적화하는 것이 효과적