# SQL
## 구성 요소

- Table(=Relation)
    - 데이터가 기록되는 곳
- Field(=Column, Attribute)
- Record(=Row, Tuple)
- Database(=Schema)
    - 테이블의 집합
- Primary Key
    - 각 레코드의 고유한 값
    - 레코드의 식별자로 사용
- Foreign Key
    - 테이블의 필드 중 다른 테이블의 레코드를 식별할 수 있는 키
    - 다른 테이블의 기본 키를 참조

## SQL 문법

1. 대소문자 구분하지 않음
    1. 대문자로 작성하는 것을 권장(명시적 구분)
2. 각 SQL문 끝에는 세미콜론 필요
3. FROM  → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT

### 목적

| 유형 | 역할 | SQL 키워드 |
| --- | --- | --- |
| DDL |  | CREATE <br> DROP <br> ALTER |
| DQL | 데이터 검색 | SELECT |
| DML | 데이터 조작(추가, 수정, 삭제) | INSERT <br> UPDATE <br> DELETE |
| DCL | 데이터 및 작업에 대한 사용자 권한 제어 | COMMIT <br> ROLLBACK <br> GRANT <br> REVOKE |
- Query: 데이터베이스로부터 정보를 요청하는 것

<aside>

### 조회

</aside>

### SELECT

```sql
SELECT Name, Milliseconds / 60000 AS '재생 시간(분)'    # 필드의 이름을 바꿀 때
FROM tracks;
```

### <정렬>

### ORDER BY

- 오름차순이 기본값
    - 오름차순 정렬 시 NULL이 먼저 출력됨

```sql
SELECT Name, Milliseconds / 60000 AS '재생 시간(분)'
FROM tracks
ORDER BY Milliseconds DESC;     # 내림차순 정렬
```

### <필터링>

### DISTINCT

```sql
SELECT DISTINCT Country    # 중복 제거
FROM customers
ORDER BY Country;
```

### WHERE

- From절 다음에 위치, 비교 연산자/논리 연산자(AND, OR, NOT 등) 사용 가능
- AND(&&), OR(||), NOT(!)

```sql
SELECT LastName, FirstName, Company, Country
FROM customers
WHERE Country = 'USA' AND Company ISNULL;    # null인지 판단하려면 ISNULL 사용

SELECT Name, Bytes 
FROM tracks
WHERE Bytes BETWEEN 10000 AND 500000    # 사잇값 찾을 때 BETWEEN ~ AND ~ 사용

SELECT LastName, FirstName, Country
FROM customers
WHERE Country IN ('Canada', 'Germany', 'France');    # 포함 여부 찾기

SELECT LastName, FirstName
FROM customers
WHERE LastName LIKE '%son';   # 맨 끝자리가 'son'인 데이터 찾기(반대는 '%son', 중간은 '%son%')
WHERE FirstName LIKE '___a';  # 맨 앞자리 3개 뒤에 'a'인 데이터 찾기
```

### LIMIT

```sql
SELECT TrackId, Name, Bytes
FROM tracks
ORDER BY Bytes DESC
LIMIT 2, 7;   # 2번째부터 7개의 데이터 조회
```

### GROUP BY

- 레코드를 그룹화 → 요약본 생성
    - 집계 함수(SUM, AVG, MAX, MIN, COUNT)와 함께 사용
- FROM, WHERE 절 뒤에 배치

```sql
SELECT Country, COUNT(*)    # 전체 데이터의 개수를 count
FROM customers;

SELECT Composer, AVG(Bytes) AS '평균'
FROM tracks
GROUP BY Composer
HAVING 평균 > 10000     # 집계 항목에 대한 세부 조건, GROUP BY가 없으면 WHERE처럼 동작
ORDER BY 평균 DESC;
```

<aside>
💡

### 생성

</aside>

- SQLite 데이터 타입
    - NULL
    - INTEGER
    - REAL
        - 부동 소수점
    - TEXT
    - BLOB
        - 이미지, 동영상, 문서 등 바이너리 데이터
- 제약 조건
    - 테이블 필드에 적용되는 규칙, 제한 사항 = 데이터 무결성, 데이터베이스 일관성 보장
    - PRIMARY KEY
        - INTEGER 타입에만 적용, 다른 정수 유형은 적용 X
    - FOREIGN KEY
        - 외래 키 관계 정의
    - NOT NULL
        - 해당 필드에 NULL값을 허용하지 않도록
        - NULL을 저장할 필요가 없는 경우가 많기에 대부분 NOT NULL로 정의
        - 값이 없다 = 0 또는 빈 문자열로 설정해두기
- AUTOINCREMENT
    - 필드의 자동 증가를 나타내는 특수 키워드
    - PK 필드에 적용
    - 새로운 레코드는 이전 최대값보다 큰 값 할당, 삭제된 값은 재사용 X

```sql
# 테이블 생성 시 정의한 필드는 기본 값이 없어도 NOT NULL 제약조건으로, DEFAULT는 NULL
CREATE TABLE examples (
  ExamId INTEGER PRIMARY KEY AUTOINCREMENT,
  LastName VARCHAR(50) NOT NULL,
  FirstName VARCHAR(50) NOT NULL
  createdAt DATE NOT NULL
  customer_id INTEGER,    # 외래 키로 사용할 필드
  FOREIGN KEY (customer_id) REFERENCES customers(customer_id)   # REFERENCES 뒤에 참조할 필드
);
```

```sql
# 이미 있는 TABLE에 필드 추가하기
# SQLite는 한 번에 여러 필드 추가 불가
ALTER TABLE examples
ADD COLUMN Country VARCHAR(100) NOT NULL DEFAULT 'default value';
```

```sql
# 생성된 테이블에 데이터 삽입
INSERT INTO articles (title, content, createdAt)
VALUES
	('title1', 'world', '2001-01-01'),
	('mytitle', 'mycontent', DATE());     # 현재 날짜를 데이터로 삽입
```

<aside>


### 수정

</aside>

```sql
# 테이블 이름 변경
ALTER TABLE examples
RENAME TO new_examples;
------------------------------------------------------------------------------------

# 필드의 이름을 변경
ALTER TABLE examples
RENAME COLUMN Address TO PostCode;
-------------------------------------------------------------------------------------
# 필드 삭제
ALTER TABLE examples
DROP COLUMN PostCode;

# 테이블 삭제
DROP TABLE new_examples;
```

```sql
# 필드와 데이터 수정
UPDATE articles
SET                                  # 수정할 필드와 새 값 지정
  title = 'update Title',
  content = 'update content'
WHERE id = 1;
-------------------------------------------------------------------------------------
# articles 테이블에서 작성일이 오래된 순으로 레코드 2개 삭제
DELETE FROM articles
WHERE id IN (
  SELECT id FROM articles
  ORDER BY 'createdAt'
  LIMIT 2
);
```

## 관계형 DB

- 테이블을 서로 연결시켜 한 테이블에서 값을 변경하면 연결된 테이블의 값도 자동으로 변경

### JOIN

- 테이블을 분리했을 때 출력 시 문제 발생(테이블 1개만 출력 가능)
    - 다른 테이블과 결합하여 출력

```sql
# INNER JOIN(교집합)
SELECT 테이블.필드
FROM 메인 테이블
INNER JOIN 조인할 테이블
	ON 조인 조건

# 1번 회원이 작성한 모든 게시글의 제목, 작성자명 조회
SELECT articles.title, users.name
FROM articles
INNER JOIN users
	ON users.id = articles.userId
WHERE users.id = 1;
-------------------------------------------------------------------------------------
# LEFT JOIN(합집합)
SELECT 테이블.필드
FROM 왼쪽 테이블
LEFT JOIN 오른쪽 테이블
	ON 조인 조건

# 왼쪽 테이블의 모든 레코드를 표기, 오른쪽 테이블과 매칭되는 레코드 없으면 NULL 표시
# 게시글 작성한 이력이 없는 회원 정보 조회
SELECT articles.name
FROM users
LEFT JOIN articles
	ON articles.userId = users.id
WHERE articles.userId IS NULL;
```