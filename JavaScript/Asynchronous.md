# 비동기
- 시간이 필요한 작업들은 백그라운드에서 실행 → 빨리 완료되는 작업부터 처리(병렬적 수행)

### Single Thread 언어

- Thread: 작업 처리할 때 실제로 작업 수행하는 주체
- multi-thread면 업무 수행하는 주체가 여러 개
- javascript가 동작할 수 있는 환경(runtime) - 브라우저 or node.js
    - 비동기 처리를 할 수 있도록 도와주는 환경이 필요

- Web API
    - 브라우저에서 제공하는 runtime 환경
    - 시간이 소요되는 작업 처리 (setTimeout, DOM Event, 비동기 요청 등)
- Task Queue
    - 비동기 처리된 Callback 함수가 대기하는 큐
- Event Loop
    - 처리할 작업이 없을 땐 쉬고, 들어오면 처리
1. 모든 작업은 Call Stack(LIFO)으로 들어간 후 처리
2. 오래 걸리는 작업은 Call Stack → Web API로 보내 별도로 처리 (3초 뒤 Task Queue로 보냄)
3. Web API에서 처리가 끝난 작업들은 Task Queue(FIFO)에 순서대로 들어감
4. Event Loop가 Call Stack이 비어 있는 것을 체크하고 비면 FIFO에 맞게 Call Stack으로 보냄

## 1. Ajax(Asynchronous JavaScript and XML)

- 하나의 특정한 기술 X → 비동기적 웹 앱 개발에 사용되는 기술들의 집합을 지칭
- XMLHttpRequest(XHR) 기술을 사용해 브라우저와 서버 간 데이터를 비동기적으로 교환
    - XML 데이터는 요즘은 안 씀 → JSON 형태를 많이 사용
    - XHR: 웹 브라우저 - 서버 간 비동기 통신을 가능하게 하는 JavaScript 객체
        - XML 뿐만 아니라 모든 종류의 데이터를 가져올 수 있음(이름만 이럼)
- 비동기 통신
    - 웹 페이지 전체를 새로고침하지 않고 서버와 데이터 주고 받기
- 부분 업데이트
    - 페이지 로드 없이 HTML 페이지 일부 DOM만 업데이트
- 서버 부하 감소
    - 필요한 데이터만 요청 → 서버 부하 감소

## 2. Axios

- 브라우저와 Node.js에서 사용할 수 있는 Promise 기반의 HTTP 클라이언트 라이브러리
- 브라우저를 위한 XHR 객체 생성
- XHR 객체 생성, 요청 / 받은 JSON 데이터를 Promise 객체 데이터를 활용해 DOM 조작

### Promise 객체

- JavaScript에서 비동기 작업을 처리하기 위한 객체
- 비동기 작업의 최종 완료(또는 실패)와 그 결과값을 나타냄
- 콜백 지옥 문제도 해결
- then() / catch()
    - 항상 promise 객체를 반환
    - then : 이전 작업의 성공 결과를 인자로 받아 callback
        - 비동기 작업의 순차적인 처리 가능 → 가독성 좋아짐
        - 위에서부터 차례대로 then 실행(Event Queue에 배치되는 순서대로 호출)
    - catch : then이 하나라도 실패하면 callback(남은 then은 중단)
        - 이전 작업의 실패 객체를 인자로 받음

```jsx
axios({
  method: 'get',
  url: 'https://api.thecatapi.com/v1/images/search'
})
  .then((response) => {            // 성공했을 때 
    console.log(response)
  })
  .catch((error) => {              // 실패했을 때 
    console.log(error)
  })
```

- then 분할
    - 계속해서 chaining 가능
        1. 비동기 작업의 순서와 의존 관계 명확히 표현 → 가독성 향상
        2. 각 작업 단계에서 발생하는 에러를 분할해서 처리
        3. 각 단계마다 다른 비동기 작업 수행 → 유연성
        4. 코드 관리 용이

```jsx
const getCats = function () {
axios({
  method: 'get',
  url: URL
})
  .then((response) => {
    // console.log(response)
    // console.log(response.data)
    // console.log(response.data[0].url)

    const imgURL = response.data[0].url
    return imgURL
  })
  .then((imgURL) => {
    const imgTag = document.createElement('img')
    imgTag.setAttribute('src', imgURL)
    document.body.appendChild(imgTag)
  })
  .catch((error) => {
    console.log(error)
    console.log('실패했다옹')
  })
console.log('야옹야옹')
}

btn.addEventListener('click', getCats)
```

### 외부 api axios 호출 방법(파라미터 존재할 때)
```jsx
const name = nameInput.value

axios({
  method: 'get',
  url: `https://api1.agify.io`,
    params: {                  // url 뒤쪽에 쿼리 매개변수를 작성해줌
    name: name                 // name=name 이렇게 요청됨(뒤 name은 nameInput값)
    }
})

// 이렇게 작성해도 됨
axios({
  method: 'get',
  url: `https://api1.agify.io?name=${name}`,
})
```