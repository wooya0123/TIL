### 에러

- 문법 에러
    - 문법 오류, 잘못된 할당
    - EOL, EOF

### 2. 예외

- 내장 예외
    - 예외 상황들이 클래스로 구현되어 있음
    - 예외 상황
        - **ZeroDivisionError**
            - **나누기 또는 모듈 연산의 두 번째 인자가 0일 때**
        - **TypeError**
            - **자료형 불일치**
        - **ValueError**
            - **인자에 잘못된 값 받음**
        - NameError
            - 이름을 찾을 수 없을 때
        - IndexError
            - 시퀀스 인덱스 범위를 벗어날 때
        - KeyError
            - 딕셔너리에 해당 키가 없는 경우
        - KeyboardInterrupt
            - 무한 루프될 때 ctrl c로 강제 종료
        - IndentationError
            - 잘못된 들여쓰기
        - ModuleNotFoundError
        - ImportError
- 예외처리 사용 구문
    - 예외 처리
        - try - except - else - finally
            - try 실행할 작업
            except 예외가 발생할 때 작업
            else 예외가 발생하지 않았을 때 작업
            finally 예외 상관없이 실행할 코드
    - 최상위 클래스를 먼저 확인하면 그 밑의 코드가 죽어버림
        - 하위 클래스부터 확인
    - as 키워드
        - 예외가 발생할 때 error 변수에 넣어서 활용 가능
        
    - if - else도 같이 쓸 수 있음

### 3. EAFP vs LBYL

- try - except는 일단 실행 후 처리
    - 예외 상황을 예측하기 어려운 경우에 사용
- if - else는 조건에 맞는지 확인 후 진행
    - 예외 상황을 미리 방지하고 싶을 때 사용