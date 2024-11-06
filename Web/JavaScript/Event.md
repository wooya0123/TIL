# 이벤트
- DOM 요소에서 event가 발생하면 해당 event는 연결된 event handler에 의해 처리

## 1. event handler

- 특정 이벤트가 발생했을 때 실행되는 함수
    - 사용자 행동에 어떻게 반응할지를 javascript 코드로 표현한 것
### DOM요소.addEventListerner(type, handler)
- type이벤트가 발생하면 handler(콜백 함수)를 호출
    - type
        - 수신할 이벤트 이름
        - 문자열로 작성(정해진 이름이 있음)
    - handler
        - 발생한 이벤트 객체를 수신하는 콜백 함수
        - 이벤트가 발생하면 이벤트 핸들러는 자동으로 event 객체를 매개변수로 받음
            - event 객체가 필요 없는 경우 생략 가능

```jsx
// 1. 버튼 선택
const btn = document.querySelector('#btn')

// 2. 콜백 함수
const detectClick = function (event) {
	event.preventDefault()    // 이벤트의 기본 동작을 막음 ex. form 제출 시 새로고침
  console.log(event)
}

// 3. 버튼에 이벤트 핸들러를 부착
btn.addEventListener('click', detectClick)
```

## 2. 버블링

- 한 요소에 이벤트가 발생하면 그 요소에 할당된 핸들러 동작 → 부모 요소 핸들러 동작
    - 최상단의 조상 요소 만날 때까지 반복

### **1. currentTarget 속성** **(=this)**

- 현재 요소
- 항상 이벤트 핸들러가 연결된 요소만을 참조하는 속성
- 이벤트가 처리되는 동안에만 사용 가능 → currentTarget 이후 속성 값들은 target 참고해서 사용

### **2. target 속성**

- 가장 안쪽의 요소를 참조하는 속성
- 실제 이벤트가 시작된 요소
- 버블링 진행되도 변하지 않음

---

- td를 클릭했을 때 최상위 요소부터 아래로 전파(캡처링)
- 실제 이벤트가 발생한 지점에서 실행
→ 다시 위로 전파(버블링)
    - 전파 과정에서 상위 요소에 할당된 이벤트 핸들러들이 호출되는 것
- 요소의 공통 조상에 이벤트 핸들러 할당
→ 여러 버튼 요소에서 발생하는 이벤트를 한 번에 다룰 수 있음
    - event.target으로 이벤트가 발생한 버튼 알 수 있음

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9aeca94d-fa8d-4099-814a-9dfb0061bed2/0b5dab98-22dc-4579-88bf-37c6bda75ee6/image.png)

## 3. 예제

```jsx
<body>
  <input type="text" class="input-text">
  <button id="btn">+</button>
  <ul></ul>

  <script>
    const inputTag = document.querySelector('.input-text')
    const btn = document.querySelector('#btn')
    const ulTag = document.querySelector('ul')

    const addTodo = function (event) {    // btn에 붙일 것이므로 event는 btn을 가리킨다
      const inputData = inputTag.value    // currentTarget 대신에 조회한 tag 사용
      
      if (inputData.trim()) {     // 문자열의 공백을 모두 지워봤을 때 문자열 
      // li 태그 생성 후 내용 변경 -> ul 태그에 넣어주기
      const liTag = document.createElement('li')
      liTag.textContent = inputData
      ulTag.appendChild(liTag)
      
      inputTag.value = ''     // 출력 후 inputTag 초기화
      } else {
        alert('todo를 입력해주세요')    // 앞에 window가 생략, 페이지 알람 표시
      }
    }

    btn.addEventListener('click', addTodo)
  </script>
</body>
```