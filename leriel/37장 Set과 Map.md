## 37.1 Set

**Set**은 중복을 허용하지 않는 값들의 컬렉션입니다. 수학의 집합과 유사한 개념이라고 생각하시면 됩니다.

### 37.1.1 Set 객체의 생성

`Set` 객체는 `new Set()` 생성자를 통해 생성합니다. 이터러블(예: 배열)을 인자로 전달하여 초기값을 설정할 수 있습니다.

```jsx
// 빈 Set 생성
const set1 = new Set();
console.log(set1); // Set(0) {}

// 배열을 이용하여 Set 생성 (중복된 값은 하나만 저장됨)
const set2 = new Set([1, 2, 3, 3, 4]);
console.log(set2); // Set(4) {1, 2, 3, 4}
```

### 37.1.2 요소 개수 확인

`Set` 객체의 요소 개수는 `size` 프로퍼티로 확인할 수 있습니다.

```jsx
const set = new Set([1, 2, 3]);
console.log(set.size); // 3 
```

### 37.1.3 요소 추가

`add()` 메서드를 사용하여 Set에 요소를 추가합니다. 이미 존재하는 요소를 추가하면 무시됩니다.

```jsx
const set = new Set();
set.add(1);
set.add(2);
set.add(2); // 중복되므로 추가되지 않음
console.log(set); // Set(2) {1, 2}
```

### 37.1.4 요소 존재 여부 확인

`has()` 메서드를 사용하여 Set에 특정 요소가 존재하는지 확인할 수 있습니다.

```jsx
const set = new Set([1, 2, 3]);
console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

### 37.1.5 요소 삭제

`delete()` 메서드를 사용하여 Set에서 특정 요소를 삭제합니다. 삭제 성공 여부를 나타내는 불리언 값을 반환합니다.

```jsx
const set = new Set([1, 2, 3]);
console.log(set.delete(2)); // true (요소 2 삭제됨)
console.log(set); // Set(2) {1, 3}
console.log(set.delete(4)); // false (요소 4는 존재하지 않음)
```

### 37.1.6 요소 일괄 삭제

`clear()` 메서드를 사용하여 Set의 모든 요소를 일괄 삭제합니다.

```jsx
const set = new Set([1, 2, 3]);
set.clear();
console.log(set); // Set(0) {}
```

### 37.1.7 요소 순회

`Set` 객체는 이터러블이므로 `for...of` 문, `forEach()` 메서드, 또는 스프레드 문법 등을 사용하여 순회할 수 있습니다.

```jsx
const set = new Set([1, 2, 3]);

// for...of 문
for (const item of set) {
  console.log(item); // 1, 2, 3
}

// forEach() 메서드
set.forEach(item => console.log(item)); // 1, 2, 3

// 스프레드 문법을 사용하여 배열로 변환
const arr = [...set];
console.log(arr); // [1, 2, 3]
```

### 37.1.8 집합 연산

`Set`을 사용하면 교집합, 합집합, 차집합 등의 집합 연산을 쉽게 구현할 수 있습니다.

**예시: 교집합 (Intersection)**

```jsx
const setA = new Set([1, 2, 3, 4]);
const setB = new Set([3, 4, 5, 6]);

const intersection = new Set([...setA].filter(x => setB.has(x)));
console.log(intersection); // Set(2) {3, 4}

```

---

## 37.2 Map

**Map**은 키-값 쌍의 컬렉션입니다. 객체와 유사하지만, 객체는 키로 문자열 또는 심벌만 허용하는 반면, Map은 모든 자료형을 키로 사용할 수 있다는 강력한 특징이 있습니다.

### 37.2.1 Map 객체의 생성

`Map` 객체는 `new Map()` 생성자를 통해 생성합니다. 이터러블(예: 배열의 배열 `[[key, value], ...]`)을 인자로 전달하여 초기값을 설정할 수 있습니다.

```jsx
// 빈 Map 생성
const map1 = new Map();
console.log(map1); // Map(0) {}

// 배열을 이용하여 Map 생성
const map2 = new Map([['name', 'Alice'], ['age', 30], [true : 30]);
console.log(map2); // Map(2) {"name" => "Alice", "age" => 30}

// 객체를 키로 사용
const objKey = { id: 1 };
const map3 = new Map([[objKey, 'value']]);
console.log(map3); // Map(1) {{...} => "value"}
```

### 37.2.2 요소 개수 확인

`Map` 객체의 요소 개수는 `size` 프로퍼티로 확인할 수 있습니다.

```jsx
const map = new Map([['a', 1], ['b', 2]]);
console.log(map.size); // 2
```

### 37.2.3 요소 추가

`set()` 메서드를 사용하여 Map에 키-값 쌍을 추가합니다. 이미 존재하는 키에 값을 설정하면 해당 키의 값이 갱신됩니다.

```jsx
const map = new Map();
map.set('name', 'Bob');
map.set('age', 25);
map.set('name', 'Charlie'); // 'name' 키의 값이 'Charlie'로 갱신됨
console.log(map); // Map(2) {"name" => "Charlie", "age" => 25}
```

### 37.2.4 요소 취득

`get()` 메서드를 사용하여 Map에서 특정 키에 해당하는 값을 취득합니다. 키가 존재하지 않으면 `undefined`를 반환합니다.

```jsx
const map = new Map([['name', 'David'], ['city', 'Seoul']]);
console.log(map.get('name')); // David
console.log(map.get('country')); // undefined
```

### 37.2.5 요소 존재 여부 확인

`has()` 메서드를 사용하여 Map에 특정 키가 존재하는지 확인할 수 있습니다.

```jsx
const map = new Map([['id', 123]]);
console.log(map.has('id')); // true
console.log(map.has('name')); // false
```

### 37.2.6 요소 삭제

`delete()` 메서드를 사용하여 Map에서 특정 키에 해당하는 키-값 쌍을 삭제합니다. 삭제 성공 여부를 나타내는 불리언 값을 반환합니다.

```jsx
const map = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(map.delete('key1')); // true
console.log(map); // Map(1) {"key2" => "value2"}
console.log(map.delete('key3')); // false
```

### 37.2.7 요소 일괄 삭제

`clear()` 메서드를 사용하여 Map의 모든 키-값 쌍을 일괄 삭제합니다.

```jsx
const map = new Map([['a', 1], ['b', 2]]);
map.clear();
console.log(map); // Map(0) {}
```

### 37.2.8 요소 순회

`Map` 객체는 이터러블이므로 `for...of` 문, `forEach()` 메서드 등을 사용하여 순회할 수 있습니다. `keys()`, `values()`, `entries()` 메서드를 사용하여 키, 값, 또는 키-값 쌍을 각각 이터레이터로 반환받아 순회할 수도 있습니다.

```jsx
const map = new Map([['apple', 10], ['banana', 20]]);

// for...of 문 (기본적으로 [key, value] 배열 반환)
for (const [key, value] of map) {
  console.log(`${key}: ${value}`); // apple: 10, banana: 20
}

// forEach() 메서드
map.forEach((value, key) => console.log(`${key}: ${value}`)); // apple: 10, banana: 20

// keys() 메서드
for (const key of map.keys()) {
  console.log(key); // apple, banana
}

// values() 메서드
for (const value of map.values()) {
  console.log(value); // 10, 20
}

// entries() 메서드 (기본 for...of와 동일)
for (const entry of map.entries()) {
  console.log(entry); // ["apple", 10], ["banana", 20]
}
```

---

**결론적으로**

- **Set:** 중복 없는 유일한 값들을 저장해야 할 때, 또는 집합 연산이 필요할 때.
- **Map:** 다양한 자료형을 키로 사용하여 데이터를 저장하고 싶을 때, 또는 삽입 순서가 중요한 키-값 쌍을 관리할 때.

---

| 특징 / 항목 | Map 객체 | 일반 객체 ({}) |
| --- | --- | --- |
| 키(Key)의 타입 | 모든 자료형을 키로 사용할 수 있습니다. (문자열, 숫자, 불리언, 객체, 함수 등) | 문자열 또는 Symbol 타입만 키로 사용할 수 있습니다. 다른 타입은 내부적으로 문자열로 변환됩니다.  |
| 순서 보장 | 요소가 삽입된 순서대로 반복(iteration)이 보장됩니다. | ES2015(ES6)부터는 정수형 키를 제외하고는 삽입 순서가 보장되지만, 공식적으로는 순서 보장을 기대하지 않는 것이 좋습니다. (실제로는 대부분 보장됨) |
| 요소 개수 확인 | size 프로퍼티를 사용하여 쉽게 요소의 개수를 얻을 수 있습니다. (map.size) | 직접적으로 요소의 개수를 얻는 프로퍼티가 없습니다. Object.keys(obj).length와 같이 메서드를 통해 확인해야 합니다. |
| 성능 (추가/삭제) | 키-값 추가 및 삭제 시 일반적으로 더 효율적입니다. 특히 많은 수의 요소를 다룰 때 유리합니다. | 키-값 추가 및 삭제 시 Map에 비해 성능상 불리할 수 있습니다. |
| 이터러블 여부 | 그 자체로 이터러블(Iterable)입니다. for...of 문으로 직접 순회 가능. (map.entries(), map.keys(), map.values()) | 그 자체로 이터러블이 아닙니다. Object.keys(), Object.values(), Object.entries()와 같은 메서드를 사용하여 순회해야 합니다. |
| 메서드 | set(), get(), has(), delete(), clear() 등 다양한 전용 메서드를 제공합니다. | 특별한 전용 메서드가 없으며, 프로토타입 체인을 통해 상속받은 메서드나 Object 정적 메서드를 사용합니다. |
| 프로토타입 체인 | 일반 객체의 프로토타입 체인에 영향을 받지 않습니다.  | Object.prototype을 상속받으므로, 프로토타입 체인에 의해 의도치 않은 프로퍼티가 존재할 수 있습니다. (예: hasOwnProperty, toString 등) |
| JSON 직렬화 | JSON.stringify()로 직접 직렬화할 수 없습니다. | JSON.stringify()로 쉽게 직렬화할 수 있습니다. |