## 1. 스타일 (중요)

필요에 따라 골라서 사용

### 인라인 스타일

- HTML 요소 안에 style 속성 값으로 작성
- 관리가 너무 어려워서 잘 사용 X

### 내부 스타일 시트

- head 태그 안에 style  태그 작성

### 외부 스타일 시트

- 별도 CSS 파일 생성 후 HTML link 태그로 불러오기

## 2. 선택자 (중요)

### 특성

1. Specificity
    - 동일한 요소를 가리키는 2개 이상의 CSS 규칙이 있는 경우 가장 높은 명시도를 가진 selector가 승리
2. Cascade
    - 한 요소에 동일한 가중치를 가진 선택자가 적용될 때 CSS에서 마지막에 나오는 선언이 사용

### 기본 선택자

1. 전체 선택자(*)
    - HTML 모든 요소 선택
2. **요소(tag) 선택자**
    - 지정한 모든 태그를 선택
    - 똑같은 태그가 너무 많기 때문에 하나를 특정하기 힘듦
3. **클래스 선택자(.)**
    - 주어진 클래스 속성을 가진 것 선택
    - 가장 자주 사용
4. **아이디 선택자(#)**
    - 해당 id를 가진 것을 선택
    - 요소 1개에만 작동
5. 속성 선택자 등

### 결합자

1. 자손 결합자(공백)
    - 첫 번째 요소의 자손 요소들 선택
    - ex. p span은 <p>안에 있는 모든 <span>을 선택
2. 자식 결합자(>)
    - 첫 번째 요소의 직계 자식만 선택
    - ex. ul > li는 <ul> 안에 있는 <li>를 선택 (한 단계 아래 자식들만)

### 명시도 순위(중요)

1. !important
    1. Cascade 구조를 무시하고 강제로 스타일 적용, 권장 X
2. Inline 스타일
3. 선택자
    1. id 선택자 > class 선택자 > 요소 선택자
    2. 대부분 class 선택자를 사용
    3. 모든 선택자보다 상속받은 게 더 약함
4. 소스 코드 선언 순서
    1. **가중치가 동일할 경우 마지막에 선언된 것이 우선순위**

## 3. 상속

- 상속 되는 속성
    - text 관련 요소
- 상속 되지 않는 속성
    - 배치에 관련된 요소
- MDN 문서
    - 상속 여부 확인 가능

## 4. CSS Box Model

- HTML의 모든 요소는 사각형
- shorthand 속성
    - border
        - 속성 적을 때 한 줄로 순서 상관없이 값만 적어도 적용됨
    - margin, padding
        - 4개: 상/우/하/좌
        - 3개: 상/좌우/하
        - 2개: 상하/좌우  (좌우에 auto를 주면 균등한 값을 줘서 중앙 정렬)
        - 1개: 공통
- box-sizing
    - content-box
        - 내가 정한 width랑 height를 content 기준으로 생각
        - 잘 안 씀
    - border-box
        - width랑 height를 테두리까지 포함해서 생각
        - CSS 시작할 때 전체 설정으로 해두고 시작함

### 박스 타입

- 박스 타입에 따라 배치 흐름 및 다른 박스와 관련해 박스가 동작하는 방식이 달라짐
1. **Outer display type**
    - block
        - 아래로 흐름
        - 오른쪽 모든 영역을 다 먹음 → 구조를 나타내기 때문
        - 너비와 높이를 조절 가능
        - padding, margin, border로 다른 요소를 상자로부터 밀어냄
    - inline
        - 오른쪽으로 흐름
        - 딱 자기 영역만 먹음
        - 너비와 높이 속성 사용 X
        - 수직은 못 밀어내고 수평은 밀어낼 수 있음
2. **Inline display type**
    - flexbox
3. **Inline-block**
    - 너비와 높이 속성 사용 가능
    - padding, margin, border로 다른 요소가 상자에서 밀려남
    - 새로운 행으로 넘어가지 않음
    - 요소가 줄 바뀌지 않으면서 너비와 높이를 적용하고 싶을 때
4. **None**
    - 해당 공간에서 표시하지 않게 함
    - JS를 통해서 화면이 줄어들었을 때 표시하지 않도록 할 때

## 5. CSS Layout

- 각 요소의 위치와 크기를 조정하여 웹페이지의 디자인 결정

## Position

### 1. 구성요소

- 페이지 특정 항목의 위치를 조정

### 2. 특징

- static
    - 요소를 Normal Flow에 따라 배치
    - top, left, bottom, left 속성 적용 X
    - 기본값
- relative
    - 요소를 Normal Flow에 따라 배치
    - 자신의 원래 static 위치 기준 top, right, bottom, left 속성으로 위치 조정
    - 자신의 원래 static 공간을 차지, 다른 요소가 원래 공간을 차지하지 않음
- absolute
    - 요소를 Normal Flow에서 제거
    - 가장 가까운 relative 부모 요소를 기준 top, right, bottom, left 속성으로 위치 조정
        - 없으면 body 태그 기준
    - 문서에서 요소가 차지하는 공간이 없어짐
    - ex. 카드에 달린 뱃지
- fixed
    - 요소를 Normal Flow에서 제거
    - 현재 화면영역(viewport) 기준 top, right, bottom, left 속성으로 위치 조정
    - 스크롤해도 항상 같은 위치에 고정
    - 문서에서 요소가 차지하는 공간 없어짐
- sticky
    - relative와 fixed의 특성을 결합한 속성
    - 스크롤이 특정 임계점에 도달하기 전까지는 relative
    - 스크롤이 특정 임계점에 도달하면 fixed처럼 화면에 고정
    - 다음 sticky 요소와 만나면 이전 sticky 요소 자리에 다음 요소로 바뀜
    
    ```css
    position: sticky;
    top:0;
    ```
    
- z-index
    - 요소 쌓임 순서를 정의
    - 값이 클수록 요소가 위에 쌓임
    - static이 아닌 요소에만 적용

## Flexbox

- 행, 열로 배치하는 1차원 레이아웃 방식

### 1. 구성요소

- main 축
    - flex item들이 배치되는 기본 축
    - main start ~ main end 방향으로 배치
- cross 축
    - main 축에 수직인 축
- flex container
    - display: flex 또는 display: inline-flex가 설정된 부모 요소
    - 이 container의 1차 자식 요소들이 flex item
    - flexbox 속성 값들로 자식 요소 flex item을 배치함
    - 아무 설정하지 않으면 flex item은 flex container의 높이만큼 차지함

### 2. 속성

**<배치>**

- **flex-direction:**
    - column
        - 기본값은 row인데 메인 축을 세로로 바꿀 수 있음
    - column-reverse; / row-reverse;
        - start와 end를 바꿈

- **flex-wrap:**
    - nowrap
        - 기본값
        - 화면이 줄어들면 그대로 줄어듦
    - wrap
        - 화면이 줄어들면 화면을 벗어나는 요소는 다음 행에 배치하여 표시
    - wrap-reverse
        - 화면을 벗어나는 요소는 wrap 한 후에 메인 축 기준으로 위-아래 반전

**<공간 분배>**

- **justify-content:**    주축 기준
    - flex-start / center / flex-end
        - flex-start가 기본값
        - start, center, end 기준으로 전체 ****요소 배치

- **align-content:**    교차 축 기준, wrap이나 wrap-reverse로 설정된 여러 행 사이의 간격 지정
    - flex-start / center / flex-end
        - 교차 축 기준 전체 요소를 start, center, end 기준으로 배치

**<정렬>**

- **align-items:**
    - flex-start / center / flex-end
        - 각 행을 해당 기준에 맞게 요소 배치

- **align-self:**
    - flex-start / center / flex-end
        - 특정 요소만 정렬

- **order:**
    - 기본값 0
    - 양수나 음수로 순서를 바꿀 수 있음

**<기타>**

- flex-grow  ↔  flex-shrink
    - 여백의 값을 비율에 따라 flex item에 분배

ex. flex-grow

- flex-basis
    - flex-item의 초기 크기 값을 지정
    - width보다 우선순위

## 6. 기타

### 마진 상쇄

- 두 요소의 마진이 충돌했을 때 두 요소 사이의 마진은 둘 중 더 큰 값으로 설정

### 수평 정렬

1. block 요소
    - margin: auto
        - 블럭 요소 너비 지정 후 좌우 마진을 auto로
2. inline 요소
    - 부모 요소에 text-align: center 적용
3. inline-block 요소
    - 부모 요소에 text-align: center 사용

### flex shorthand

- 추가 조사 필요