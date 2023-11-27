# SQL기본문법

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
## 연산자

| 의미            | 연산자                                                 | 설명                                                                             |
| --------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------- |
| +, -, \*, /     |                                                        |                                                                                  |
| 나머지 연산자   | %, MOD                                                 |                                                                                  |
| 참/거짓 연산자  | TRUE(1), FALSE(0)                                      |                                                                                  |
| 비교 연산자     | IS(=), IS NOT(!=)                                      |                                                                                  |
| 논리 연산자     | AND(&&), OR(\|)                                        |                                                                                  |
| 사잇값          | BETWEEN {MIN} AND {MAX}<br>NOT BETWEEN {MIN} AND {MAX} | MIN과 MAX를 포함하여 그 사이에 있는 값이면 TRUE를 반환                           |
| 포함값          | IN {값1, 값2, 값3, ...)<br>NOT IN {값1, 값2, 값3, ...) | 값1, 값2, 값3, ...에 해당하는 값이면 TRUE를 반환                                 |
| 문자열 대체     | LIKE '...%...'  <br>LIKE '...\_...'                    | %자리에는 문자 0~N개가 들어갈 수 있고  <br>\_자리에는 문자 1개가 대체될 수 있음  |
| Contains 연산자 | Contains                                               | 두 집합을 비교하여 한 집합이 다른 집합 내에 모든 원소들을 포함하면 TRUE를 반환함 |

#### 1) 사칙연산 및 나머지연산
간단한 사칙연산 + - \* / 4가지에  
%로 나머지연산도 되고 %는 MOD라고 영어로 써도 됨

```sql
SELECT 1 + 2;
-- 이러면 애트리뷰트 이름은 1+ 2로 나오고 밑에 값은 3으로 나옴
```

| **1 + 2** |
| --------- |
| 3         |

```sql
SELECT 5 - 2.5 AS DIFFERENCE;
-- AS는 위에서 배운 대로 alias 별명만드는것
-- 즉 애트리뷰트는 DIFFERENCE라고 나오고 밑에 값이 2.5가 나옴
```

| **DIFFERENCE** |
| -------------- |
| 2.5            |

```sql
SELECT 3 * (2 + 4) / 2, 'Hello';
-- 애트리뷰트는 3 * (2 + 4) / 2와 Hello 두개가 생기고
-- 값은 계산된 결과인 9.0000과 Hello가 들어감
```

```sql
SELECT 10 % 3;
SELECT 10 MOD 3;
-- 둘 다 같은 계산이지만 
-- 윗줄처럼 하면 애트리뷰트 이름이 10 % 3이 나오고 
-- 아랫줄처럼 하면 애트리뷰트값이 10 MOD 3이 나옴
-- 하지만 결과값은 1로 같음
```

#### 2) 문자열 연산
문자열에 사칙연산을 가하면 문자열을 0취급함  
단, 숫자로 이루어진 문자열은 해당 숫자로 취급함  
C++의 string이나 js같은곳에선 ABC3 으로 될수도 있겠지만 sql은 0으로 취급해버린다는 차이점 알아두기  

```sql
SELECT 'ABC' + 3;
-- 이러면 ABC문자열이 0취급되므로 결과는 3이 나옴
```

```sql
하지만 'ABC'가 아니라 '1'이라면 해당 숫자로 취급함
SELECT '1' + '002' * 3;
-- 숫자로 인식해서 1 + 2 * 3으로 결과는 7이 나오게 됨
```

그냥 무의미한 문자열이 아니라 애트리뷰트명이라면 둘을 연산해줌
```sql
SELECT
  OrderID, ProductID, OrderID + ProductID AS SUM
FROM OrderDetails;
-- 이렇게 되면 OrderID와 ProductID와 둘이 더해진 값들이 나옴
```

활용예시)
```sql
SELECT
  ProductName,
  Price / 2 AS HalfPrice
FROM Products;
-- 이렇게 하면 HalfPrice라는 이름으로 반값들을 볼 수 있음
```

```sql
SELECT
  ProductName,
  Price,
  Price / 2 AS HalfPrice,
  Price * 2 AS DoublePrice,
  Price * 0.75 AS SalePrice
FROM Products;
-- 이렇게 쓰면 제품명, 원가, 반값, 2배값, 25%세일가 이렇게 다 보기 편하게도 볼 수 있음
```

#### 3) 참/거짓 연산자  
TRUE(1) FALSE(0) 두개로 사용하며 !로 not기능도 사용 가능, !는 NOT이라고 문자로도 사용 가능

```sql
SELECT TRUE, FALSE;
-- 이러면 컬럼명은 TRUE FALSE 두개가 나옴
-- 값은 1 0 이 나옴
```

| TRUE | FALSE |
| ---- | ----- |
| 1    | 0     |

```sql
SELECT !TRUE, NOT 1, !FALSE, NOT FALSE;
-- 컬럼명은 위 문구들 대로 나옴
-- 값은 0 0 1 1 이 나오게 되겠지
```

| !TRUE | NOT 1 | !FALSE | NOT FALSE |
| ----- | ----- | ------ | --------- |
| 0     | 0     | 1      | 1         |

```sql
SELECT 0 = TRUE, 1 = TRUE, 0 = FALSE, 1 = FALSE;
-- 값은 0 1 1 0이 나오게 되는 것
```

| 0 = TRUE | 1 = TRUE | 0 = FALSE | 1 = FALSE |
| -------- | -------- | --------- | --------- |
| 0        | 1        | 1         | 0         |

```sql
SELECT * FROM Customers WHERE TRUE;
-- where은 조건명시하는 곳인데 이렇게 true를 넣으면 항상 참이므로 나오게 되지만
SELECT * FROM Customers WHERE FALSE;
-- 이렇게 FALSE를 넣으면 항상 거짓이므로 아무 값도 안나오게 됨
```
  
#### 4) 비교연산자  
IS(=)와 IS NOT(!=) // 같을때 \=\=가 아닌 = 한개만 쓰는것에 주의  
!=, <> 양쪽 값이 다름   // <>도 있는거 알아두기  
\>, < 비교연산  
\>=, <= 크거나 같다, 작거나 같다.

```sql
SELECT TRUE IS TRUE;
-- 참인 문장이므로 값이 1이 나옴
-- SELECT TRUE = TRUE;과도 같은 의미임
```

```sql
SELECT TRUE IS NOT FALSE;
-- 참인 문장이므로 값이 1이 나옴
-- SELECT TRUE != FALSE;와 같은 의미임
```

```sql
SELECT (TRUE IS FALSE) IS NOT TRUE;
-- 이렇게도 사용 가능
```

```sql
SELECT 1 = 1, !(1 <> 1), NOT (1 < 2), 1 > 0 IS NOT FALSE;
-- 1 1 0 1이 나옴
```

```sql
SELECT 'A' = 'A', 'A' != 'B', 'A' < 'B', 'A' > 'B';
-- 1 1 1 0이 나옴
```

```sql
SELECT 'Apple' > 'Banana', 1 < 2 IS TRUE;
-- 0 1이 나옴
```
문자열간의 비교는 아스키코드랑 똑같게 생각하면 됨  0
단 MySQL의 기본 사칙연산자는 대소문자를 구분하지 않으므로 SELECT 'A' = 'a';처럼 쓰면 1임  
  
테이블의 컬럼이 아닌 값을 SELECT하는 예시)
```sql
SELECT
  ProductName, Price,
  Price > 20 AS EXPENSIVE 
FROM Products;
-- 이렇게 되면 애트리뷰트가 ProductName Price EXPENSIVE 3개가 나오게 되고
-- 값들은 ProductName Price는 쭉 나오고 EXPENSIVE쪽은 조건에 맞으면 1, 아니면 0이 쭉 나옴
```

```sql
SELECT
  ProductName, Price,
  NOT Price > 20 AS CHEAP 
FROM Products;
-- 위에꺼에서 말만 반대로 바꾼 것
```

#### 5) 논리연산자
AND(&&)와 OR(||)

```sql
SELECT TRUE AND FALSE, TRUE OR FALSE;
-- 이렇게 되면 TRUE AND FALSE는 0이고
-- TRUE OR FALSE는 1이 되겠지
-- AND는 &&로, OR는 ||로 사용 가능하므로
-- SELECT TRUE && FALSE, TRUE || FALSE; 와 같음
```

|   |   |
|---|---|
|TRUE AND FALSE|TRUE OR FALSE|
|0|1|

```
SELECT 2 + 3 = 6 OR 2 * 3 = 6;
-- 이건 1이 되고
```

```
-- 일반적인 AND의 쓰임
SELECT * FROM Orders
WHERE
  CustomerId = 15 AND EmployeeId = 4;
-- 두 조건을 모두 만족하는 조건의 값을 가져 오게끔
```

```
-- 일반적인 OR의 쓰임
SELECT * FROM Products 
WHERE
  ProductName = 'Tofu' OR CategoryId = 8;
```

```
SELECT * FROM OrderDetails
WHERE
  ProductId = 20 AND (OrderId = 10514 OR Quantity = 50);
-- 이렇게도 사용 가능
```