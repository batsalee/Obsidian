# DQL

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

#### 2) SELECT문에 조건달기(WHERE)
```SQL
SELECT * FROM Orders
WHERE EmployeeID = 3;
-- Orders 테이블에서 EmployeeID가 3인 조건에 맞는것들만 모두 가져온다
```

```SQL
SELECT * FROM OrderDetails 
WHERE Quantity < 5;
-- Quantity가 5보다 작은 값들만 가져온다
```

#### 3) 정렬(ORDER BY)  
ASC : 오름차순이며 기본값  
DESC : 내림차순

```SQL
SELECT * FROM Customers
ORDER BY ContactName;
-- ASC나 DESC가 안붙어있으니 기본적으로 오름차순으로 정렬이며
-- 정렬 기준은 ContactName을 기준으로 정렬
```

```SQL
SELECT * FROM OrderDetails
ORDER BY ProductID ASC, Quantity DESC;
-- 정렬 기준을 두가지로 정할 수 도 있음. 하나는 오름차순으로 다른건 내림차순으로
-- 물론 위에서 ASC는 빼도 됨
```
  
#### 4) 원하는 갯수/범위만큼만 데이터 가져오기(LIMIT)  
\*로 모든 데이터를 가져오면 DB에 부하를 줄 수 있기때문에 몇개만 가져오게 하려는 경우  
`LIMIT {가져올 갯수}` 또는 `LIMIT {건너뛸 갯수}, {가져올 갯수}`처럼 사용

```SQL
SELECT * FROM Customers
LIMIT 10;
-- 이렇게 쓰면 Customers테이블에 데이터가 아무리 많아도 처음의 10개만 가져옴
```

```SQL
SELECT * FROM Customers
LIMIT 0, 10;
-- 이렇게 쓰면 0번째까지는 건너띄고 10개 가져오라는 뜻이므로 그냥 LIMIT 10과 같음
```

```SQL
SELECT * FROM Customers
LIMIT 30, 10;
-- 이렇게 쓰면 30개는 건너뛴 후 10개를 가져오라는 뜻이므로 31번데이터부터 40번데이터까지 가져옴
-- 주로 게시판에 10개의 글은 1페이지에, 또 다음 10개의 글은 2페이지에 나타내는 식으로 나눠 쓰기 위해 사용됨
```

#### 5) 원하는 별명(alias)로 가져오기  
AS를 사용해서 컬럼명을 변경 할 수 있음
```SQL
SELECT
  CustomerId AS ID,
  CustomerName AS NAME,
  Address AS ADDR
FROM Customers;
-- 이렇게 쓰면 애트리뷰트 이름이 CustomerID 대신 그냥 ID로 뜨는 식으로 가져 올 수 있음
```

```SQL
SELECT
  CustomerId AS '아이디',
  CustomerName AS '고객명',
  Address AS '주소'
FROM Customers;
-- 이런식으로 한글로도 보기 쉽게 만들 수 있음
```

>[!note] SELECT문 정리
> 지금까지 배운 내용 전부 다 쓴 것
> ```sql
> SELECT
> 	CustomerID AS '아이디',
> 	CustomerName AS '고객명',
> 	City AS '도시',
> 	Country AS '국가'
> FROM Customers
> WHERE
> 	City = 'London' OR Country = 'Mexico'
> ORDER BY CustomerName
> LIMIT 0, 5;
> ```
