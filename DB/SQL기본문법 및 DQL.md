# SQL기본문법 및 DQL

## 기본 문법

#### 1) 주석
SQL에서 주석 다는 법은 `-- `뒤에 쓰면 됨(`--`뒤에 한칸 띄어야 함)
```sql
-- 주석사용 예시
```

#### 2) 대소문자
SQL 구문인 SELECT FROM WHERE 등은 대문자로 쓰던 소문자로 쓰던 상관없음  
보기 편하게, 쓰기 편하게 사용하면 됨  

#### 3) 공백
공백은 스페이스, 탭, 엔터 모두 의미없음
이 또한 보기 편하게, 쓰기 편하게 사용하면 됨  

___
## SELECT 문

DQL(데이터 질의언어, Data Query Language)  
SELECT문을 말하며 SELECT문을 DQL로 따로 빼기도 하지만 대부분 DML에 포함시켜서 말함  

정보 검색문이며 아래와 같은 형식으로 작성  
```SQL
-- SELECT와 FROM은 필수로 포함되어야 함
SELECT <attribute list>	             -- SELECT절에서는 지정된 애트리뷰트만 잘라냄   
FROM <table list>                    -- FROM에서 테이블들을 카티션곱해서 WHERE절로
[WHERE <condition>]                  -- WHERE절에서는 검색조건을 만족하는 튜플만 GROUP BY로
[GROUP BY <grouping attribute(s)>]   -- GROUP BY절에서는 튜플들을 그룹으로 분할하여 HAVING절로
[HAVING <group condition>]           -- HAVING절에서는 그룹별 검색조건을 만족하는 그룹만 ORDER BY절로
[ORDER BY <attribute list>]          -- ORDER BY 절에서는 순서대로 정렬 한 후 SELECT절로 반환
```

#### 1) 기본적인 SELECT문

```SQL
SELECT * FROM Customers;
-- Customers 테이블의 모든 column(열)을 보는 것
```

```SQL
SELECT CustomerName FROM Customers;
-- Customers 테이블의 CustomerName열만 보는 것
```

```SQL
SELECT CustomerName, ContactName, Country
FROM Customers;
-- Customers 테이블에서 CustomerName, ContactName, Country 이렇게 3개의 열을 보는 것
```

```SQL
SELECT
  CustomerName, 1, 'Hello', NULL
FROM Customers;
-- 테이블의 컬럼이 아닌 값도 선택할 수 있음
-- 프로그래밍할때 사용하기 위해 있는 기능

-- 아래의 표처럼 결과가 나오는데
-- CustomerName은 있는 데이터니까 잘 검색 되고 
-- 1, 'Hello'은 애트리뷰트 이름도, 값도 다 1로 쭉, Hello로 쭉 나옴
-- NULL은 열 이름만 NULL이고 데이터는 빈칸으로 나옴
```

| **CustomerName**                   | **1** | **Hello** | **NULL** |
| ---------------------------------- | ----- | --------- | -------- |
| Ana Trujillo Emparedados y helados | 1     | Hello     |          |
| Antonio Moreno Taquería            | 1     | Hello     |          |
| Around the Horn                    | 1     | Hello     |          |
| Berglunds snabbköp                 | 1     | Hello     |          |
| Blauer See Delikatessen            | 1     | Hello     |          |

