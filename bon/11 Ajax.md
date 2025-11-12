Asynchronous JavaScript and XML
자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고,
서버가 응답한 데이터를 수신하여 웹페이지를 "동적으로 갱신"하는 프로그래밍 방식

Ajax는 브라우저에서 제공하는 Web API인 XMLHttpRequest 객체를 기반으로 동작한다.

Ajax의 등장으로 웹의 패러다임이 획기적으로 전환 되었다.
> 이전에는 화면이 통으로 깜빡이며 업데이트 되었으나 
> Ajax덕분에 브라우저에서도 데스크톱 애플리케이션과 유사한 빠른 퍼포먼스와 부드러운 화면 전환이 가능해짐


이전
- 변경할 필요가 없는 부분까지 포함한 완전한 HTML을 서버로부터 매번 다시 전송받아 렌더링 -> 불필요한 데이터 통신 발생
- 처음부터 HTML을 매번 다시 렌더링하므로 -> 깜빡임 현상 발생 + 불필요한 렌더링 리소스 소모
- 클라이언트 <-> 서버 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있을 때 까지 브라우저 화면은 로딩상태(블로킹)

Ajax 이후
- 갱신이 필요한 데이터만 서버로부터 전송받음
- 변경 필요가 없는 부분은 렌더링하지 않음 -> 깜빡임 현상 발생하지 않음
- 클라이언트 <-> 서버 통신이 비동기 방식으로 동작하여 서버에 요청을 보낸 이후 블로킹 발생하지 않음

---

## XMLHttpRequest

자바스크립트를 사용하여 HTTP요청을 전송하려면
XMLHttpRequest 객체를 사용한다.
XMLHttpRequest는 Web API이며, HTTP요청 전송과 HTTP 응답 수신을 위한 메서드와 프로퍼티를 제공한다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();
```


아래의 메서드,프로퍼티는 모두 인스턴스 프로퍼티

### readyState

- HTTP 요청의 현재 상태를 나타내는 정수.
- UNSENT : 0
	- XMLHttpRequest객체가 만들어졌지만 `open()` 호출 전 상태
	- 요청 준비도 안 됨
- OPENED : 1
	- `open()`메서드가 호출된 상태
	- 아직 `send()` 호출 안 됨
	- 요청을 설정해둔 상태로, 헤더 수정 가능
- HEADERS_RECEIVED : 2
	- `send()`가 호출되고 서버에서 응답 헤더를 받음
	- HTTP 상태코드(`status`)랑 응답 헤더를 읽을 수 있음
- LOADING : 3
	- 응답 본문(response body)를 다운로드 중
	- `responseText`에 데이터가 일부 들어오기 시작함. 
- DONE : 4
	- 요청 완료. 응답 데이터 전체를 사용할 수 있음
	- 성공이면 `status = 200`, 실패라면 404, 500같은 코드 확인 가능.



### status

- HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수
- ex) 200


### statusText

- HTTP 요청에 대한 응답 메시지를 나타내는 문자열
- ex) OK


### responseType

- HTTP 응답 타입
- ex) document, json, text, blob, arraybuffer


### 이벤트 핸들러 프로퍼티

| 이벤트 핸들러 프로퍼티       | 설명                                      |
| ------------------ | --------------------------------------- |
| onreadystatechange | readyState 프로퍼티 값이 변경된 경우               |
| onloadstart        | HTTP 요청에 대한 응답을 받기 시작한 경우               |
| onprogress         | HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생          |
| onabort            | abort 메서드에 의해 HTTP 요청이 중단된 경우           |
| onerror            | HTTP 요청에 에러가 발생한 경우                     |
| onload             | HTTP 요청이 성공적으로 완료한 경우                   |
| ontimeout          | HTTP 요청 시간이 초과한 경우                      |
| onloadend          | HTTP 요청이 완료한 경우. HTTP 요청이 성공 또는 실패하면 발생 |


### 메서드

| 메서드               | 설명                        |
| ----------------- | ------------------------- |
| open              | HTTP 요청 초기화               |
| send              | HTTP 요청 전송                |
| abort             | 이미 전송된 HTTP 요청 중단         |
| setRequestHeader  | 특정 HTTP 요청 헤더의 값을 설정      |
| getResponseHeader | 특정 HTTP 요청 헤더의 값을 문자열로 반환 |

---

## HTTP요청 전송

1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청을 초기화
2. 필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 헤더 값 설정
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP요청 전송

```js
GET 예시
// 객체 생성
const xhr = new XMLHttpRequest();

// HTTP요청 초기화
xhr.open('GET', '/users');

// HTTP요청 헤더 설정
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send();
```

```js
POST 예시
// 객체 생성
const xhr = new XMLHttpRequest();

// HTTP요청 초기화
xhr.open('POST', '/users');

// HTTP요청 헤더 설정
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send(JSON.stringify({ id: 1, content: 'Hello world'}));
```

- `setRequestHeader` 메서드는 반드시 `open`메서드를 호출한 이후에 호출해야 함
- `xhr.setRequestHeader('content-type', 'application/json')`
	- `content-type` : 요청 몸체에 담아 전송할 데이터의 mime타입을 명시할 때 사용하는 헤더
		- 즉 클라이언트 측에서 "내가 보내는 데이터는 이런 타입이야" 라고 말해주는 것
- `xhr.setRequestHeader('accept', 'application/json')`
	- `accept` : 서버가 응답할 데이터의 mime타입을 명시할 때 사용하는 헤더
		- 즉, 클라이언트 측에서 "내가 이 요청을 보내면 응답 데이터는 이런 타입으로 보내줘" 라고 말해주는 것

---

## HTTP 응답 처리

서버가 전송한 응답을 처리하려면 XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해야 한다.

```js
const xhr = new XMLHttpRequest();

// HTTP요청 초기화
xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

// HTTP 요청 전송
xhr.send();

xhr.onreadystatechange = () => {
	// 서버 응답이 아직 완료되지 않았다면 아무런 처리를 하지 않는다
	if (xhr.readyState !== XMLHttpRequest.DONE) return;
	
	if (xhr.status === 200) {
		console.log(JSON.parse(xhr.response));
	} else {
		console.error ( 'Error', xhr.status, xhr.statusText);
	}
}
```

