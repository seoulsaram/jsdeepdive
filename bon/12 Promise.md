ppt자료 보기
```js
  let response;
  
  const xhr = new XMLHttpRequest();
  xhr.open("GET", "https://jsonplaceholder.typicode.com/posts/1");
  xhr.send();

  xhr.onload = () => { //onload 이벤트는 비동기로 실행됨
    if (xhr.status === 200) {
      response = JSON.parse(xhr.response);
      console.log(response) // ??
    }
    console.error(`(${xhr.status}) ${xhr.statusText}`);
   };
   
	console.log("response : ", response); //??
```

위 코드의 로그를 예측해보자.

---

## 기존 비동기 함수의 한계

위 예제에서 맨 밑의 console.log는 undefined이 나온다.
xhr의 onload는 서버의 응답이 올때까지 기다리는 비동기 이벤트 핸들러이기 때문이다.

🧐 이런 경우 비동기 처리 결과를 외부에 반환하거나, 
상위 스코프의 변수에 할당하려면 어떻게 해야 할까?

> 방법이 없음. 비동기 함수 처리 결과에 대한 후속 처리를 비동기 함수 내부에서 수행해야 함.

💡 그래서 나온 일반적인 패턴 : 콜백 함수를 전달하기
```js
function get(url, successCallback, failureCallback) {
	const xhr = new XMLHttpRequest();
	xhr.open("GET", url);
	xhr.send();
	
	xhr.onload = () => {
		if (xhr.status === 200) {
			successCallback(JSON.parse(xhr.response));
		} else {
			failureCallback(`(${xhr.status}) ${xhr.statusText}`);
		}
	};
}


const response = get("https://jsonplaceholder.typicode.com/posts/1", console.log, console.error);
```


##### 1. 콜백 헬

위의 패턴으로 처리할 경우 :

 >비동기 함수가 비동기 처리 결과를 가지고
 >또다시 비동기 함수를 호출해야 한다면
 >=> 콜백 헬
 
```js
// 콜백헬 예시
get("/api/user/1", (user) => {
	console.log("User:", user);
	// 1단계 끝나고 2단계 호출
	get(`/api/user/${user.id}/posts`, (posts) => {
		console.log("Posts:", posts);
		// 2단계 끝나고 3단계 호출
		get(`/api/post/${posts[0].id}/comments`, (comments) => {
			console.log("Comments:", comments);
			// 3단계 끝
			console.log("모든 데이터 가져오기 완료!");
		}, (err) => {
			console.error("댓글 가져오기 실패:", err);
		});
	}, (err) => {
		console.error("포스트 가져오기 실패:", err);
});
```


##### 2. 에러처리의 한계
ppt자료 보기
```js
try {
  setTimeout(()=> {throw new Error('Error')},1000);
} catch(e){
  //캐치 안됨
  console.error('에러 캐치', e)
}
```

1. setTimeout은 비동기 함수이므로 콜백 함수(`throw new Error('Error')`)가 호출되는 것을 기다리지 않고 즉시 종료 되어 콜스택에서 제거 됨
2. 이후 타이머가 만료되면 setTimeout함수의 콜백 함수(`throw new Error('Error')`)는 태스크 큐로 푸시되고,
3. 이는 콜스택이 비워지면 이벤트 루프에 의해 콜스택으로 푸시되어 실행 됨
4. setTimeout함수의 콜백(`throw new Error('Error')`)이 실행될 때 setTimeout함수는 이미 콜스택에서 제거된 상태
5. 즉 setTimeout함수의 콜백(`throw new Error('Error')`)함수를 호출한 것이 setTimeout함수가 아니라는 것을 의미한다.
6. 에러는 호출자(caller) 방향으로 전파된다. (즉, 콜스택 아래 방향)
7. 하지만 setTimeout함수의 콜백 함수를 호출한 것은 setTimeout이 아니므로, setTimeout 함수의 콜백 함수가 발생키신 에러는 catch 블록에서 캐치되지 않는다.

위 예제를 catch하게 만들려면 아래와같이 하면 됨
```js
try{

	setTimeout(()=> {
		try{
			throw new Error('Error')
		}catch(e){
			console.error('에러 캐치', e)
		}
	},1000)
	
}catch(e){
	console.error('에러 캐치', e)
}
```

---

## 이런 한계를 극복하기 위해 나온 Promise

Promise생성자 함수는 비동기 처리를 수행할 콜백 함수를 인수로 전달받는다.
이 콜백 함수는 resolve와 reject함수를 인수로 전달받는다.

```js
const promise = new Promise((resolve, reject)=> {
  if(true){ /* 비동기 성공일 경우 */
    resolve('result');
  }else{ /* 비동기 실패일 경우 */
    reject('failed')
  }
});

promise.then(console.log);
```


🧩 퀴즈 : 아래 예제 promise패턴으로 바까보기
```js
function get(url, successCallback, failureCallback) {
	const xhr = new XMLHttpRequest();
	xhr.open("GET", url);
	xhr.send();
	
	xhr.onload = () => {
		if (xhr.status === 200) {
			successCallback(JSON.parse(xhr.response));
		} else {
			failureCallback(`(${xhr.status}) ${xhr.statusText}`);
		}
	};
}


const response = get("https://jsonplaceholder.typicode.com/posts/1", console.log, console.error);
```

👉 답
```js
const promiseGet = (url) => {
  return new Promise((resolve, reject)=> {
	const xhr = new XMLHttpRequest();
	xhr.open("GET", url);
	xhr.send();
  
  xhr.onload = () => {
	  if (xhr.status === 200) {
			resolve(JSON.parse(xhr.response));
		} else {
			reject(`(${xhr.status}) ${xhr.statusText}`);
		}
	};
});
}

promiseGet("https://jsonplaceholder.typicode.com/posts/1")
.then(console.log)

//콜벡헬 개선 예시
promiseGet("/api/user/1")
  .then((user) => {
    console.log("User:", user);
    return promiseGet(`/api/user/${user.id}/posts`);
  })
  .then((posts) => {
    console.log("Posts:", posts);
    return promiseGet(`/api/post/${posts[0].id}/comments`);
  })
  .then((comments) => {
    console.log("Comments:", comments);
    console.log("모든 데이터 가져오기 완료!");
  })
  .catch((err) => {
    console.error("에러 발생:", err);
  });
```


프로미스 상태

- 생성된 직후의 프로미스는 기본적으로 pending 상태
- 이후 resolve함수가 호출되면 fulfilled 상태가 됨
- 이후 reject함수가 호출되면 reject 상태가 됨
- fulfilled, reject 상태를 settled상태라고 한다.

프로미스는 pending상태에서 fulfilled, reject 상태로 변화할 수 있지만,
일단 settled상태가 되면 더는 다른 상태로 변화할 수 없다.


![[스크린샷 2025-09-10 오전 10.11.09.png]]

프로미스는 비동기 처리 상태와 더불어
비동기 처리 결과도 상태로 갖는다.

```js
const fulfilled = new Promise(resolve => resolve(1))
console.log(fulfilled);
```

![[스크린샷 2025-09-10 오전 10.11.49.png]]


```js
const rejected = new Promise((_, reject) => reject(1))
console.log(rejected)
```

![[스크린샷 2025-09-10 오전 10.13.10.png]]

> 즉, 프로미스는 비동기 처리 상태와 처리 결과를 관리하는 객체다


---

## Promise.prototype.then, catch, finally

- then, catch, finally 메서드는 언제나 프로미스를 반환한다.
- 만약 메서드들의 콜백 함수가 프로미스를 반환하면 그 프로미스를 그대로 반환하고,
- 콜백 함수가 프로미스가 아닌 값을 반환하면 그 값을 암묵적으로 resolve 또는 reject하여 프로미스를 생성해 반환한다.


then메서드의 콜백 함수가 프로미스를 반환하는 경우
```js
Promise.resolve(1)
  .then((num) => {
    // 프로미스를 반환 → 다음 then은 이 프로미스의 resolve값을 받음
    return new Promise((resolve) => setTimeout(() => resolve(num + 1), 1000));
  })
  .then((num) => {
    console.log("두 번째 then:", num); // 2
  })
  
```


then메서드의 콜백 함수가 프로미스가 아닌 값을 반환하는 경우
```js
Promise.resolve(1)
  .then((num) => {
    console.log("첫 then:", num); // 1
    // 그냥 숫자를 반환 → 내부적으로 Promise.resolve(2)로 감싸짐
    return num + 1;
  })
  .then((num) => {
    console.log("두 번째 then:", num); // 2
  });
```

---

## 프로미스의 정적 메서드

### `Promise.resolve` / `Promise.reject`

정적 메서드인 resolve, reject는 이미 존재하는 값을 래핑하여
프로미스를 생성하기 위해 사용한다.

```js
const resolvedPromise = Promise.resolve([1,2,3]);
resolvedPromise.then(console.log)
```


언제 사용?

case1. 일관된 인터페이스 유지할 때
```js
// 유저 정보를 가져오는 API 유틸
function fetchUser(id?: string) {
  if (!id) {
    // id가 없으면 바로 null을 반환하되, 일관적으로 Promise로 감싸기
    return Promise.resolve(null);
  }

  return fetch(`/api/users/${id}`).then((res) => {
    if (!res.ok) {
      // 에러 응답이면 reject
      return Promise.reject(new Error("사용자 정보를 가져오지 못했습니다"));
    }
    return res.json();
  });
}

// 사용처
fetchUser("123")
  .then((user) => {
    if (user) console.log("유저:", user.name);
  })
  .catch((err) => console.error("에러:", err.message));
```
➡️ API 호출이든 아니든 항상 `Promise`를 반환하므로 `await`/`.then` 으로 통일된 처리가 가능.


case2. 테스트/실험 기능 관리에 사용할 때 (network request를 mock하고싶을때)
```js
function runFeature(flag: boolean) {
  if (!flag) {
    return Promise.reject(new Error("이 기능은 비활성화 상태입니다."));
  }
  return Promise.resolve("기능 실행 성공!");
}

// 사용처
runFeature(false)
  .then((msg) => console.log(msg))
  .catch((err) => console.error("실패:", err.message))
```
➡️ 실행 조건이 맞지 않으면 즉시 reject, 아니면 resolve.

---

## 마이크로 테스크 큐

프로미스의 후속 처리 메서드의 콜백 함수는 (then문)
태스크 큐가 아니라 마이크로 태스크 큐에 저장된다.

마이크로태스크 큐는 태스크큐보다 우선순위가 높다.

이벤트 루프는 콜스택이 비면
- 먼저 마이크로태스크 큐에서 대기하고 있는 함수를 가져와 실행함
- 이후 마이크로태스크 큐가 비면 태스크 큐에서 대기하고 있는 함수를 가져와 실행함

🧩 퀴즈 : 출력 결과 순서는?
```js
setTimeout(()=> console.log(1),0);

Promise.resolve()
	.then(()=> console.log(2))
	.then(()=> console.log(3))
```

<details> <summary>정답 보기</summary> 2->3->1 </details>

---

## fetch

fetch함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환한다.

fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 프로미스를 반환하므로
후속 처리 메서드 then을 통해 프로미스가 resolve한 객체를 전달받을 수 있다.

```js
//출력해보자
fetch("https://jsonplaceholder.typicode.com/posts/1").then(console.log)

//res가 Response 객체이므로 Response객체의 프로퍼티/메서드를 사용할 수 있다
fetch("https://jsonplaceholder.typicode.com/posts/1").then((res)=> {
	console.log(res.status)
	return res.json();
})
```

- fetch 함수가 반환하는 프로미스는 기본적으로
- 404, 500와 같은 HTTP 에러가 발생해도 에러를 reject하지 않고 불리언 타입의 ok 상태를 false로 설정한 Response 객체를 resolve한다.
- 오프라인 등의 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우에만 프로미스를 reject한다.
- 따라서 fetch를 사용할 때는 `response.ok` 상태를 확인해 명시적으로 에러를 처리해야 한다.

404, 500와 같은 HTTP 에러가 발생해도 에러를 reject하지 않음
```js
const wrongURl = "https://jsonplaceholder.typicode.com/postss/10000";
fetch(wrongURl)
  .then((res) => {
    console.log(res.status); //404
  })
  .catch(() => {
	// 404인데 캐치 안 됨
    console.log("error");
  });
```

`response.ok` 상태를 확인해 명시적으로 에러를 처리
```js
const wrongURl = "https://jsonplaceholder.typicode.com/postss/10000";
fetch(wrongURl)
  .then((res) => {
    if(!res.ok){
      throw new Error(res.statusText);
    };
    return res.json();
  })
```

