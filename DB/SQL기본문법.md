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

| TRUE AND FALSE | TRUE OR FALSE |
| -------------- | ------------- |
| 0              | 1             |

```sql
SELECT 2 + 3 = 6 OR 2 * 3 = 6;
-- 이건 1이 되고
```

```sql
-- 일반적인 AND의 쓰임
SELECT * FROM Orders
WHERE
  CustomerId = 15 AND EmployeeId = 4;
-- 두 조건을 모두 만족하는 조건의 값을 가져 오게끔
```

```sql
-- 일반적인 OR의 쓰임
SELECT * FROM Products 
WHERE
  ProductName = 'Tofu' OR CategoryId = 8;
```

```sql
SELECT * FROM OrderDetails
WHERE
  ProductId = 20 AND (OrderId = 10514 OR Quantity = 50);
-- 이렇게도 사용 가능
```

  
#### 6) 사잇값  
BETWEEN과 NOT BETWEEN으로 사용하며 어떤 값이 어떤 값들 사이에 있다 아니다

```sql
BETWEEN {MIN} AND {MAX}; -- MIN과 MAX 사이에 있다
NOT BETWEEN {MIN} AND {MAX}; -- MIN과 MAX 사이에 없다
--MIN과 MAX 두 숫자도 포함해서 검색함
```

```sql
SELECT 5 BETWEEN 1 AND 10;
-- 5가 1과 10 사이에 있으니 결과는 1이 나옴

-- 반면 이렇게 1과 10 순서를 바꾸면 결과가 0이 나옴
SELECT 5 BETWEEN 10 AND 1;
-- 즉 작은 숫자가 반드시 앞에 있어야 제대로 된 결과를 기대 할 수 있음
```

```sql
SELECT 'banana' BETWEEN 'Apple' AND 'camera';
-- 문자열에도 사용 가능
```

```sql
SELECT * FROM OrderDetails
WHERE ProductID BETWEEN 1 AND 4;
-- 조건문에서도 사용 가능
```

```sql
SELECT * FROM Customers
WHERE CustomerName BETWEEN 'b' AND 'c';
-- 이렇게 CustomerName이 B로 시작하는 애들만 골라 낼 수도 있음
```

#### 7) 포함값  
IN과 NOT IN  
IN (값1, 값2, 값3, ...), NOT IN (값1, 값2, 값3, ...) 처럼 사용

```sql
SELECT 1 + 2 IN (2, 3, 4) 
SELECT 'Hello' IN (1, TRUE, 'hello') 
SELECT * FROM Customers WHERE City IN ('Torino', 'Paris', 'Portland', 'Madrid') 
-- 위 세 문장처럼 사용
-- 1번문장처럼 연산자 사용도 가능하고
-- 2번 문장에서 대소문자 상관없이 사용 하는것도 확인 가능하고
-- 3번 문장에서 범위지정이 힘든 문자열들을 나열해서 사용하는 것도 가능
```

#### 8) 문자열 대체  
LIKE를 사용하며 완벽히 일치하는게 아니라 몇글자 일치하면 가져오게 할때  
LIKE '문자열형태' 처럼 LIKE 뒤에 '  ' 문자열 모양으로 씀

LIKE '...%...'     %자리에 0~N개의 문자를 가진 패턴  
LIKE '...\_...'       \_을 쓴 갯수만큼의 문자를 가진 패턴

```sql
SELECT
  'HELLO' LIKE 'hel%',
  'HELLO' LIKE 'H%',
  'HELLO' LIKE 'H%O',
  'HELLO' LIKE '%O',
  'HELLO' LIKE '%HELLO%',
  'HELLO' LIKE '%H',
  'HELLO' LIKE 'L%'
-- 이렇게 쓰면 %는 글자 갯수 상관없이 위치만 맞으면 되니 1 1 1 1 1 0 0 으로 결과가 나옴
-- 다른 문장들은 % 자리에 0~N개의 문자가 들어가면 맞으니 1이고
-- 밑에서 2개의 문장만 HELLO 완성이 불가능하니 FALSE가 되는 것
-- 5번째꺼는 %HELLO%지만 %에는 0개가 올수도 있으니 TRUE가 되는 것
```

```sql
SELECT
  'HELLO' LIKE 'HEL__',
  'HELLO' LIKE 'h___O',
  'HELLO' LIKE 'HE_LO',
  'HELLO' LIKE '_____',
  'HELLO' LIKE '_HELLO',
  'HELLO' LIKE 'HEL_',
  'HELLO' LIKE 'H_O'
-- 이건 _자리에 문자 한개가 대응되서 맞으면 참이 되는 개념이므로 결과는 1 1 1 1 0 0 0이 나옴
-- %와 달리 5번째문장에 주의해야 함 _HELLO는 H앞에 문자가 하나 더 와야만 하므로 FALSE가 되는 것
```

```sql
SELECT * FROM Employees
WHERE Notes LIKE '%economics%'

SELECT * FROM OrderDetails
WHERE OrderID LIKE '1025_'
-- 위 두개의 SELECT문처럼 조건을 달아서 사용 가능
-- 숫자도 문자열처럼 ''로 감싸서 사용하면 됨
```

#### 9) Contains 연산자  
두 집합을 비교하여 한 집합이 다른 집합 내에 모든 원소들을 포함하면 TRUE를 반환함

```sql
SELECT FName, LName
FROM Employee
WHERE ( 
	SELECT PNO
	FROM WorksOn
	WHERE SSN=ESSN 

	Contains

	SELECT PNumber
	FROM Project
	WHERE DNum=5 
)
```

___
## 함수

아래에는 자주 사용되는 함수들만 설명 정리해둔거고 더 자세히 보고 싶다면 아래 링크로  
[https://dev.mysql.com/doc/refman/8.0/en/numeric-functions.html](https://dev.mysql.com/doc/refman/8.0/en/numeric-functions.html)

#### 1) 숫자 관련 함수들

| 의미                      | 연산자                                                    | 설명                                                              |
| ------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------- |
| 반올림, 올림, 내림        | ROUND<br>CEIL<br>FLOOR                                    |                                                                   |
| 절대값                    | ABS                                                       |                                                                   |
| 괄호 안에서 최대값/최소값 | GREATEST(값1, 값2, 값3, ...)<br>LEAST(값1, 값2, 값3, ...) | 괄호 안의 값들 중 최대값/최소값                                   |
| 속성의 최대값/최소값      | MAX<br>MIN                                                | MAX(Quantity)라고 한다면 Quantity값들 중 최대값<br>MIN은 최소값   |
| 거듭제곱과 루트           | POW, POWER<br>SQRT                                        | POW(A, B)라고 한다면 A를 B만큼 제곱<br>SQRT(A)는 A의 제곱근(루트) |
| 소수점 n자리까지만 표기   | TRUNCATE(N, n)                                            | N을 소수점 n자리까지만 가져오기                                   |

ROUND 반올림  
CEIL 올림              ceil은 천장이라는 뜻  
FLOOR 내림          floor은 바닥이라는 뜻  
```sql
SELECT 
  ROUND(0.5),
  CEIL(0.4),
  FLOOR(0.6);
-- 1 1 0이 됨
```

```sql
SELECT 
  Price,
  ROUND(price),
  CEIL(price),
  FLOOR(price)
FROM Products;
-- 처럼 함수로 값을 변환해서 사용 가능
```

ABS 절대값
```sql
SELECT ABS(1), ABS(-1), ABS(3 - 10);
-- 1 1 7이 나옴
```

```sql
SELECT * FROM OrderDetails
WHERE ABS(Quantity - 10) < 5;
-- Quantity가 10과 차이가 5이하인 경우를 가져오게끔
```
 
GREATEST와 LEAST  
밑에서 나오는 MAX/MIN과는 다른 개념, GREATEST와 LEAST는 괄호안에서 찾는것  
GREATEST (값1, 값2, ...) : 괄호 안 값들 중 가장 큰 값  
LEAST (값1, 값2, ...) : 괄호 안 값들 중 가장 작은 값  
```sql
SELECT 
  GREATEST(1, 2, 3),
  LEAST(1, 2, 3, 4, 5);
-- 3과 1이 결과로 나옴
```

```sql
SELECT
  OrderDetailID, ProductID, Quantity,
  GREATEST(OrderDetailID, ProductID, Quantity),
  LEAST(OrderDetailID, ProductID, Quantity)
FROM OrderDetails;
-- 이런식으로 사용 가능
```

MAX와 MIN
위의 것과 다르게 애트리뷰트 값들 중 가장 큰것, 가장 작은것을 골라옴
```sql
SELECT
  MAX(Quantity),
  MIN(Quantity),
  COUNT(Quantity),
  SUM(Quantity),
  AVG(Quantity)
FROM OrderDetails
WHERE OrderDetailID BETWEEN 20 AND 30;
```

POW와 SQRT  
POW(A, B) 또는 POWER(A, B) A를 B만큼 제곱  
SQRT(A) A의 제곱근(루트)  
※ sqrt(16)은 4가 나오고, 2분의 1승도 루트를 의미하니까 pow(16, 1/2)로 해도 4가 나옴
```sql
SELECT
  POW(2, 3),
  POWER(5, 2),
  SQRT(16);
```

```sql
SELECT Price, POW(Price, 1/2)
FROM Products
WHERE SQRT(Price) < 4;
```

TRUNCATE(N, n)  
N을 소수점 n자리까지만 가져오기
```sql
SELECT
  TRUNCATE(1234.5678, 1),
  TRUNCATE(1234.5678, 2),
  TRUNCATE(1234.5678, 3),
  TRUNCATE(1234.5678, -1),
  TRUNCATE(1234.5678, -2),
  TRUNCATE(1234.5678, -3);
-- 마이너스면 해당 자리는 0으로 처리함
-- 예를들어 제일 밑에꺼는 1000, 밑에서 두번째는 1200, 밑에서 세번째는 1230 으로 값이 나옴
```

```sql
SELECT Price FROM Products
WHERE TRUNCATE(Price, 0) = 12;
```

#### 2) 문자열 관련 함수들  

| 의미               | 연산자                                           | 설명                                                                                                                                                                                                             |
| ------------------ | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 대문자로/소문자로  | UCASE, UPPER  <br>LCASE, LOWER                   | UPPER('abcDEF')라면 모두 대문자가 되므로 ABCDEF가 반환  <br>LOWER는 소문자로                                                                                                                                     |
| 문자열 이어붙이기  | CONCAT(...)  <br>CONCAT_WS(S, ...)               | CONCAT('HELLO', ' ', 'WORLD')라면 HELLO WORLD를 반환  <br>CONCAT_WS는 S로 ...에 있는 값들을 이어붙임  <br>예를들어 CONCAT_WS('-', 2023, 03, 21)이라면 2023-03-21을 반환                                          |
| 문자열 일부 자르기 | SUBSTR, SUBSTRING  <br>LEFT  <br>RIGHT           | SUBSTR('문자열', N, n)라면 문자열의 N번째 글자부터 n개 가져옴  <br>LEFT('문자열', n)이라면 해당 문자열의 왼쪽에서 n개만 잘라서 가져옴  <br>RIGHT('문자열', n)이라면 해당 문자열의 오른쪽에서 n개만 잘라서 가져옴 |
| 문자열의 길이      | LENGTH  <br>CHAR_LENGTH  <br>(=CHARACTER_LENGTH) | LENGTH는 문자열의 바이트길이를 반환  <br>CHAR_LENGTH는 문자 자체의 길이를 반환                                                                                                                                   |
| 공백 제거          | TRIM  <br>LTRIM  <br>RTRIM                       | 양쪽 공백 제거  <br>왼쪽 공백 제거  <br>오른쪽 공백 제거                                                                                                                                                         |
| 문자열 채워붙이기  | LPAD(S, N, P)  <br>RPAD(S, N, P)                 | LPAD('ABC', 5, '-')라면 5글자가 될때까지 왼쪽에 -를 붙이므로 --ABC가 됨  <br>RPAD('ABC', 5, '-')라면 오른쪽에 -를 붙이므로 ABC--가 됨                                                                            |
| 문자열 대체        | REPLACE(S, A, B)                                 | S중 A를 B로 변경                                                                                                                                                                                                 |
| 문자열 탐색        | INSTR(S, s)                                      | S중에 s의 첫 위치 반환, 없다면 0                                                                                                                                                                                 |
| 자료형 캐스팅      | CAST(A AS T), CONVERT(A, T)                      | 01' = '1'은 FALSE가 반환되지만  <br>CAST('01' AS DECIMAL) = CASE('1' AS DECIMAL)이라면 TRUE를 반환                                                                                                               |
 
UCASE, UPPER 모두 대문자로  
LCASE, LOWER 모두 소문자로  
```sql
SELECT
  UPPER('abcDEF'),
  LOWER('abcDEF');
```

```sql
SELECT
  UCASE(CustomerName),
  LCASE(ContactName)
FROM Customers;
-- 결과값을 대문자형태로 볼지, 소문자 형태로 볼지 정하고싶을 때
```

CONCAT(...) 괄호 안의 내용을 이어붙임(string객체의 + 기능처럼 문자열 이어붙이는 기능)  
CONCAT_WS(S, ...) 괄호 안의 내용을 S로 이어붙임
```sql
SELECT CONCAT('HELLO', ' ', 'THIS IS ', 2021)
-- HELLO THIS IS 2021로 이어진 문장으로 값이 나옴
```

```sql
SELECT CONCAT_WS('-', 2021, 8, 15, 'AM')
-- 값들이 -로 이어붙여져서 2021-8-15-AM의 결과가 나옴
```

```sql
SELECT CONCAT('O-ID: ', OrderID) FROM Orders;
-- 이렇게 쓰면 O-ID: ~~~ 형태로 printf 출력문 지정하듯이 쓸 수 있음
```

```sql
SELECT
  CONCAT_WS(' ', FirstName, LastName) AS FullName
FROM Employees;
-- 이름은 이런식으로 풀네임으로 만들 수도 있고
```

SUBSTR, SUBSTRING 주어진 값에 따라 문자열 자름  
LEFT 왼쪽부터 N글자  
RIGHT 오른쪽부터 N글자
```sql
SELECT
  SUBSTR('ABCDEFG', 3),
  SUBSTR('ABCDEFG', 3, 2),
  SUBSTR('ABCDEFG', -4),
  SUBSTR('ABCDEFG', -4, 2);
-- c랑 똑같게 생각하면 됨, ABC CD DEFG DE가 출력됨
-- 마이너스는 뒤에서부터라고 생각하면 됨 뒤에서부터 4글자부터 읽으니 DEFG
-- -4, 2는 뒤에서부터 4칸인 D부터 2개
```

```sql
SELECT
  LEFT('ABCDEFG', 3),
  RIGHT('ABCDEFG', 3);
-- LEFT는 ABC가, RIGHT는 EFG가 나옴
```

```sql
SELECT
  OrderDate,
  LEFT(OrderDate, 4) AS Year,
  SUBSTR(OrderDate, 6, 2) AS Month,
  RIGHT(OrderDate, 2) AS Day
FROM Orders;
-- OrderDate가 2011-10-16 이라면 년 월 일로 뜯어서 보고 싶으면 위처럼 쓸 수 있음
```

  
LENGTH : 문자열의 바이트 길이  
CHAR_LENGTH, CHARACTER_LENGTH : 그냥 생각하는 문자열의 문자 길이  
바이트 길이는 환경마다 처리 방식이 다르다고 함  
한글을 2바이트로 기대하는데 3으로 처리하기도 하다보니 그냥 MySQL에서는 글자 길이를 원하는 경우엔 CHAR_LENGTH를 쓴다고 생각하기
```sql
SELECT
  CHAR_LENGTH('ABCDE'),
  CHAR_LENGTH('안녕하세요')
-- 이렇게 쓰면 둘 다 5로 나옴
-- 한글도 5글자라고 처리해서 5가 나옴
-- 한글을 다른 프로그램 언어처럼 10으로 나오게 하고 싶어도 LENGTH가 환경마다 다르다고 해서 확인해봐야할듯
```
  
TRIM 양쪽 공백 재거  
LTRIM 왼쪽 공백 제거  
RTRIM 오른쪽 공백 제거
```sql
SELECT
  CONCAT('|', ' HELLO ', '|'),
  CONCAT('|', LTRIM(' HELLO '), '|'),
  CONCAT('|', RTRIM(' HELLO '), '|'),
  CONCAT('|', TRIM(' HELLO '), '|');
-- | HELLO |      |HELLO |      | HELLO|        |HELLO| 순으로 나옴
-- 그냥 문자열의 왼쪽이나 오른쪽에 스페이스를 지워야 하는 경우
-- 주로 검색할때 제일 앞이나 뒤에 스페이스바를 넣고 검색하는 경우 
-- 공백이 있으면 문자열이 일치하지 않으니 결과가 안나올 수 있으니 그걸 방지하기 위해
```

```sql
-- 사용예시
SELECT * FROM Categories
WHERE CategoryName = ' Beverages '
-- 이렇게 문자열 앞에 스페이스가 있으면 아무것도 안나오겠지만

SELECT * FROM Categories
WHERE CategoryName = TRIM(' Beverages ')
-- 위 처럼 써주면서 방지가 가능함
```