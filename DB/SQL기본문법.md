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

패드  
LPAD(S, N, P) S글자가 N글자가 될때까지 P를 이어붙임  
RPAD(S, N, P) S글자가 N글자가 될때까지 P를 이어붙임
```sql
SELECT
  LPAD('ABC', 5, '-'),
  RPAD('ABC', 5, '-');
-- LPAD는 ABC가 5글자가 될때까지 왼쪽에 -를 붙이므로 결과는 --ABC가 됨
-- RPAD는 결과가 ABC--가 됨
-- 사용하는 때는 제품ID가 그냥 1 내지는 10 이렇게 되있는거를 모두 000001 혹은 000010 형태로 맞추고 싶을 때
```

REPLACE(S, A, B) S중 A를 B로 변경
```sql
SELECT
  REPLACE('맥도날드에서 맥도날드 햄버거를 먹었다.', '맥도날드', '버거킹');
-- 결과는 버거킹에서 버거킹 햄버거를 먹었다.로 나옴

SELECT
  REPLACE(Description, ', ', ' and ')
FROM Categories;
-- ,를 and로 일괄변경하고 싶을때 씀
```

INSTR(S, s) S중 s의 첫 위치 반환, 없으면 0
```sql
SELECT
  INSTR('ABCDE', 'ABC'),
  INSTR('ABCDE', 'BCDE'),
  INSTR('ABCDE', 'C'),
  INSTR('ABCDE', 'DE'),
  INSTR('ABCDE', 'F');
-- 1 2 3 4 0이 나옴

SELECT * FROM Customers
WHERE INSTR(CustomerName, ' ') BETWEEN 1 AND 6;
-- firstname이 1~5글자인 사람을 찾는 것
-- < 6이 아니라 1~6으로 비트윈 한 이유는 스페이스가 아예 없으면 0이 리턴되므로 그 경우도 포함되버림
-- 즉 한단어의 긴 글자도 포함하게 되버리므로 그게 아니려면 비트윈 사용
```
  
CAST(A AS T) A를 T자료형으로 변환  
CONVERT(A, T) A를 T자료형으로 변환
```sql
SELECT
  '01' = '1',
  CAST('01' AS DECIMAL) = CAST('1' AS DECIMAL);
-- 01과 1은 숫자가 아니라 문자인 상태
-- 즉 문자인 상태에선 ==의 답은 FALSE임
-- 하지만 둘을 숫자로 바꾼다면 둘다 1이 되므로 ==의 답은 TRUE임
```

```sql
SELECT
  '01' = '1',
  CONVERT('01', DECIMAL) = CONVERT('1', DECIMAL);
```

#### 3) 시간/날짜 관련 함수들

| 의미                                        | 연산자                                                                        | 설명                                                                                                                                                                                                                                                                      |
| ------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 현재 시간/날짜 반환                         | CURRENT_DATE, CURDATE  <br>CURRENT_TIME, CURTIME  <br>CURRENT_TIMESTAMP, NOW  | 현재 날짜 반환  <br>현재 시간 반환  <br>현재 시간과 날짜 반환                                                                                                                                                                                                             |
| 시간/날짜 객체 생성                         | DATE('2023-03-21')  <br>TIME('01:02:03')  <br>TIME(n)  <br>TIMESTAMP          | 날짜객체  <br>시간객체  <br>hh:mm:ss:nnnnnnnn에서 n의 갯수  <br>DATE + TIME = yyyy-mm-dd hh:mm:ss                                                                                                                                                                         |
| 주어진 DATETIME에서  <br>년/월/일/요일 반환 | YEAR  <br>MONTH  <br>DAY, DAYOFMONTH  <br>WEEKDAY  <br>MONTHNAME  <br>DAYNAME | 인자로 주어진 DATETIME에서 연도 추출  <br>인자로 주어진 DATETIME에서 월 추출  <br>인자로 주어진 DATETIME에서 일 추출  <br>인자로 주어진 DATETIME에서 요일 추출(월요일이 0)  <br>인자로 주어진 DATETIME에서 해당월의 영문명 반환  <br>인자로 주어진 DATETIME의 요일명 반환 |
| 주어진 DATETIME에서  <br>시/분/초 반환      | HOUR  <br>MINUTE  <br>SECOND                                                  | 시/분/초 반환                                                                                                                                                                                                                                                             |
| 날짜에 +/- 연산                             | ADDDATE, DATE_ADD  <br>SUBDATE, DATE_SUB                                      | 시간/날짜에 값 더하기  <br>시간/날짜에 값 빼기                                                                                                                                                                                                                            |
| 시간 차이 명시                              | INTERVAL                                                                      | 둘의 시간차이인 상대시각을 명시  <br>시간끼리 더하고빼거나  <br>시각에 시간을 더하고 빼서 다른시간을 만들거나                                                                                                                                                             |
| 두 DATETIME의 차이 계산                     | DATE_DIFF  <br>TIME_DIFF                                                      | 인자로 주어진 두 날짜의 차이 반환  <br>인자로 주어진 두 시간의 차이 반환                                                                                                                                                                                                  |
| 해당 월의 마지막 날짜                       | LAST_DAY                                                                      | 해당 월의 마지막 날짜(28일이거나 30일이거나 31일이거나)                                                                                                                                                                                                                   |
| DATETIME의 형식을 지정                      | DATE_FORMAT                                                                   | %Y, %y, %M, %m등등의 형식으로 printf문 만들듯이 형식 변환                                                                                                                                                                                                                 |
| DATETIME을 해석해서 객체생성                | STR_TO_DATE(S, F)                                                             | STR_TO_DATE('2021-06-04 07:48:52', '%Y-%m-%d %T')처럼 해석해서 객체생성                                                                                                                                                                                                   |
  
CURRENT_DATE, CURDATE : 현재 날짜 반환  
CURRENT_TIME, CURTIME : 현재 시간 반환  
CURRENT_TIMESTAMP, NOW : 현재 시간과 날짜 반환  
보통 앞의 것들은 길어서 뒤의 것들로 씀  
또한 예약어가 아니라 함수이므로 뒤에 괄호를 써줘야 함
```sql
SELECT CURDATE(), CURTIME(), NOW();
```
  
DATE : 문자열에 따라 날짜 생성 // 날짜 객체를 생성한다고 생각하면 더 쉬움  
TIME : 문자열에 따라 시간 생성 // 시간 객체를 생성한다고 생각하면 더 쉬움  
함수보다는 생성자에 가까운 느낌
```sql
SELECT
  '2021-6-1' = '2021-06-01',
  DATE('2021-6-1') = DATE('2021-06-01'),
  '1:2:3' = '01:02:03',
  TIME('1:2:3') = TIME('01:02:03');
-- 이건 0 1 0 1의 결과가 나옴
-- 첫줄은 문자열이 다르니까 FALSE고 두번째줄은 날짜 객체를 만들기때문에 TRUE가 되는것
```

```sql
SELECT
  '2021-6-1 1:2:3' = '2021-06-01 01:02:03',
  DATE('2021-6-1 1:2:3') = DATE('2021-06-01 01:02:03'),
  TIME('2021-6-1 1:2:3') = TIME('2021-06-01 01:02:03'),
  DATE('2021-6-1 1:2:3') = TIME('2021-06-01 01:02:03'),
  DATE('2021-6-1') = DATE('2021-06-01 01:02:03'),
  TIME('2021-6-1 1:2:3') = TIME('01:02:03');
-- 첫줄은 FALSE
-- 둘째줄 셋째줄은 같은 객체가 되니 TRUE
-- 4째줄은 왼쪽은 날짜를 가져갔으니 2021 6 1 객체고, 오른쪽은 시간을 가져갔으니 1 2 3 객체이므로 FALSE가 됨
-- 즉 '2021-6-1 1:2:3' 문자열을 TIME이나 DATE함수에 넣어도 그 부분만 잘 알아서 가져가준다는 뜻
```

```sql
-- 사용 예시
SELECT * FROM Orders
WHERE
  OrderDate BETWEEN DATE('1997-1-1') AND DATE('1997-1-31');
```

YEAR 주어진 DATETIME값의 년도 반환  
MONTH 주어진 DATETIME값의 월 반환  
DAY, DAYOFMONTH 주어진 DATETIME값의 날짜(일) 반환  

WEEKDAY 주어진 DATETIME값의 요일값 반환(월요일: 0)  

MONTHNAME 주어진 DATETIME값의 월(영문) 반환  
DAYNAME 주어진 DATETIME값의 요일명 반환
```sql
SELECT
  OrderDate,
  YEAR(OrderDate) AS YEAR,
  MONTH(OrderDate) AS MONTH,
  DAY(OrderDate) AS DAY,
  WEEKDAY(OrderDate) AS WEEKDAY,
  MONTHNAME(OrderDate) AS MONTHNAME,
  DAYNAME(OrderDate) AS DAYNAME
FROM Orders;
-- OrderDate가 1996-07-04인 경우
-- 1996 7 4 4 3 July Thursday 결과가 나옴
```

```sql
SELECT
  OrderDate,
  CONCAT(
    CONCAT_WS(
      '/',
      YEAR(OrderDate), MONTH(OrderDate), DAY(OrderDate)
    ),
    ' ',
    UPPER(LEFT(DAYNAME(OrderDate), 3))
  )
FROM Orders;
-- 1996-07-04 1996/7/4 THU 처럼 나옴
-- 이런식으로 다양하게 결합해서 사용하는 듯
```

```sql
SELECT * FROM Orders
WHERE WEEKDAY(OrderDate) = 0;
-- 월요일만 뽑아내기
```

HOUR 주어진 DATETIME의 시 반환  
MINUTE 주어진 DATETIME의 분 반환  
SECOND 주어진 DATETIME의 초 반환
```sql
SELECT
  HOUR(NOW()), MINUTE(NOW()), SECOND(NOW());
-- now()하면 현재 시간이 나오므로 거기서 시 분 초를 뽑아냄
```

ADDDATE, DATE_ADD 시간/날짜에 값 더하기  
SUBDATE, DATE_SUB 시간/날짜에 값 빼기
```sql
SELECT 
  ADDDATE('2021-06-20', INTERVAL 1 YEAR),
  ADDDATE('2021-06-20', INTERVAL -2 MONTH),
  ADDDATE('2021-06-20', INTERVAL 3 WEEK),
  ADDDATE('2021-06-20', INTERVAL -4 DAY),
  ADDDATE('2021-06-20', INTERVAL -5 MINUTE),
  ADDDATE('2021-06-20 13:01:12', INTERVAL 6 SECOND);
-- INTERVAL 문구 뒤 수식대로 처리, 첫 문장은 2021-06-20에서 1년 추가하는 것
-- 주로 어떤 사용자의 사용 권한이 만료되었는지 아닌지 판단할때 사용
```

위에껀 값에 연산을 하는 개념이었다면 아래는 두 값의 차이를 계산  
DATE_DIFF 두 시간/날짜 간 일수차  
TIME_DIFF 두 시간/날짜 간 시간차
```sql
SELECT
  OrderDate,
  NOW(),
  DATEDIFF(OrderDate, NOW())
FROM Orders;
```

```sql
SELECT
  TIMEDIFF('2021-06-21 15:20:35', '2021-06-21 16:34:41');
```

```sql
-- 사용 예시
SELECT * FROM Orders
WHERE
  ABS(DATEDIFF(OrderDate, '1996-10-10')) < 5;
```

LAST_DAY 해당 달의 마지막 날짜  
```sql
SELECT
  OrderDate,
  LAST_DAY(OrderDate),
  DAY(LAST_DAY(OrderDate)),
  DATEDIFF(LAST_DAY(OrderDate), OrderDate)
FROM Orders;
-- 이렇게 쓰면 1996-07-04 1996-07-31 31 27순으로 나오게 되고
-- 마지막날의 날짜만 빼와서 요일을 알수도 있고 등 다양하게 사용 가능
```
  
DATE_FORMAT 시간/날짜를 지정한 형식으로 반환  
%Y 년도 4자리  
%y 년도 2자리  
%M 월 영문  
%m 월 숫자  
%D 일 영문(1st, 2nd, 3rd...)  
%d, %e 일 숫자 (01 ~ 31)  
%T hh:mm:ss  
%r hh:mm:ss AM/PM  
%H, %k 시 (~23)  
%h, %l 시 (~12)  
%i 분  
%S, %s 초  
%p AM/PM
```sql
SELECT
  DATE_FORMAT(NOW(), '%M %D, %Y %T'),
  DATE_FORMAT(NOW(), '%y-%m-%d %h:%i:%s %p'),
  DATE_FORMAT(NOW(), '%Y년 %m월 %d일 %p %h시 %i분 %s초');
```

```sql
SELECT REPLACE(
  REPLACE(
    DATE_FORMAT(NOW(), '%Y년 %m월 %d일 %p %h시 %i분 %초'),
    'AM', '오전'
  ),
  'PM', '오후'
)
-- 리플레이스 조건을 두개 달고싶으면 이런식으로 처리하나 봄
```

STR_TO_DATE(S, F) S를 F형식으로 해석하여 시간/날짜 생성
```sql
SELECT
  DATEDIFF(
    STR_TO_DATE('2021-06-04 07:48:52', '%Y-%m-%d %T'),
    STR_TO_DATE('2021-06-01 12:30:05', '%Y-%m-%d %T')
  ),
  TIMEDIFF(
    STR_TO_DATE('2021-06-04 07:48:52', '%Y-%m-%d %T'),
    STR_TO_DATE('2021-06-01 12:30:05', '%Y-%m-%d %T')
  );
-- 위의 DATEDIFF는 '%Y-%m-%d %T'형태로 날짜를 받아가서 둘의 차이를 보니 3일차이
-- 아래의 TIMEDIFF는 '%Y-%m-%d %T'형태로 시간을 받아가서 둘의 시간차이
-- 단 단지 07:48:52와 12:30:05의 차이뿐 아니라 1일 12시30분05초 ~ 4일 7시 48분 52초로 계산
-- 3             67:18:47 결과로 나옴
```
  
#### 4) 기타 함수들

| 의미              | 연산자             | 설명                               |
| ----------------- | ------------------ | ---------------------------------- |
| IF 조건문         | IF(조건, T, F)     | 조건이 맞으면 T를, 틀리면 F를 반환 |
| 조건이 여러개일때 | CASE / WHEN / ELSE | C언어의 switch문과 유사            |
| if null           | IFNULL(A, B)       | A가 NULL이면 B 출력                |

IF(조건, T, F) 조건이 참이면 T를, 거짓이면 F를 반환
```sql
SELECT
  IF (1 > 2, '1는 2보다 크다.', '1은 2보다 작다.');
```

조건이 참 거짓 두가지가 아니라 여러가지인 경우는  
CASE를 사용(C언어의 switch 개념)
```sql
SELECT
  Price,
  IF (Price > 30, 'Expensive', 'Cheap'),
  CASE
    WHEN Price < 20 THEN '저가'
    WHEN Price BETWEEN 20 AND 30 THEN '일반'
    ELSE '고가'
  END
FROM Products;
```

IFNULL(A, B) A가 NULL일 시 B 출력
```sql
SELECT
  IFNULL('A', 'B'),
  IFNULL(NULL, 'B');
-- 테이블 JOIN할때 NULL값이 많이 생길 수 있으니 거기서 사용됨
```

___
## 조건에 따라 그룹으로 묶기

MAX 가장 큰 값  
MIN 가장 작은 값  
COUNT 갯수 (NULL값 제외)  
SUM 총합  
AVG 평균 값  
이것들을 GROUP BY로 집계된 곳에서 쓰게 됨  
  
#### 1) GROUP BY
- 조건에 따라 집계된 값을 ==정렬된 결과로== 가져옴
- 튜플들을 여러 부분집단으로 나누고 각 부분집단마다 집단함수 적용할 수 있음
- 예를들어 1번팀원들의 점수 평균, 2번팀원들의 점수 평균 이런걸 따로 알고 싶을 때

```sql
-- 각 부서별로 부서번호, 종업원의 수, 평균 봉급을 검색하라.
SELECT DNo, COUNT(*), AVG(Salary)
FROM Employee
GROUP BY DNo
```

```sql
SELECT Country FROM Customers
GROUP BY Country;
-- 만약 GROUP BY 없이 SELECT Country FROM Customers만 쓰게 된다면 같은 나라 이름도 여러번 나옴
-- GROUP BY를 써서 겹치지 않게 뽑아냄
-- 즉 그룹으로 묶어서 결과를 가져와줌
```

```sql
-- 여러 컬럼을 기준으로 그룹할 수도 있음
SELECT 
  Country, City,
  CONCAT_WS(', ', City, Country)
FROM Customers
GROUP BY Country, City;
-- 만약 GROUP BY를 쓰지 않았다면 이 또한 중복해서 여러번 나옴
```

```sql
-- 위에서 MAX MIN COUNT 등등을 GROUP BY와 함께 쓴다고 했는데 어떤 형식인가
SELECT
  COUNT(*), OrderDate
FROM Orders
GROUP BY OrderDate;
-- 이렇게 쓰면 주문 날짜로 그룹해서 검색하므로 해당 날짜에 주문이 몇건이었는가를 볼 수 있게 됨
```

| COUNT(\*) | OrderDate  |
| -------- | ---------- |
| 1        | 1996-07-05 |
| 2        | 1996-07-08 |
| 1        | 1996-07-09 |
| 1        | 1996-07-10 |

```sql
SELECT
  ProductID,
  SUM(Quantity) AS QuantitySum
FROM OrderDetails
GROUP BY ProductID
ORDER BY QuantitySum DESC;
-- ProductID로 그룹화를 한다는 것은 sum을 하면 해당 productID의 값들만 sum하는 것
-- 즉 productID가 1인 제품의 값들만 전부 sum, 2인 제품의 값들만 모두 sum
```

즉 MAX, MIN, COUNT, SUM, AVG 함수들이 group by와 함께 쓰이는 이유는  
그냥 sum을 하면 해당 db의 모든 값을 sum하는 것이지만  
group by와 함께 쓴다면 해당 제품의 총 판매량, 해당 직원의 총 근무시간 등을 분리해서 볼 수 있음  
  
#### 2) HAVING
그룹화된 데이터 걸러내기  

어떤 조건들을 만족하는 그룹들에게만 집단함수 적용  
즉 GROUP BY에 조건문을 다는 것  
```sql
SELECT
  Country, COUNT(*) AS Count
FROM Suppliers
GROUP BY Country
HAVING Count >= 3;
-- 즉 Country로 묶어서 각 Country의 갯수를 가져오되, 3이상인 것만 가져오게 조건을 다는 것
-- 즉 그룹바이에 대한 조건을 다는 것
```
※ WHERE는 그룹하기 전 데이터, HAVING은 그룹 후 집계에 사용  
그냥 WHERE는 GROUP BY보다 위에 있고, HAVING은 GROUP BY보다 밑에 있다고 생각하면 됨  
WHERE로 먼저 조건 걸러내고, 거기서 걸러진 조건으로 그룹화하고, 또 그 그룹에서 HAVING으로 조건 거르는 것

#### 3) WITH ROLLUP  
마지막행에 합계를 나타내줌  
ORDER BY와는 함께 사용될 수 없음  
```sql
SELECT
  Country, COUNT(*)
FROM Suppliers
GROUP BY Country
WITH ROLLUP;
```

#### 4) DISTINCT - 중복된 값들을 제거합니다.
SELECT와 함께 사용  
SQL은 모든 애트리뷰트 값이 동일한 튜플을 여러개 가질 수 있음  
정확히 말해서 SQL은 튜플의 집합이 아니라 튜플의 다중집합임  
그래서 검색하면 똑같은 내용이 여러줄 나오기도 함  
ALL(디폴트값)이나 Distinct를 이용해서 튜플 하나만 나오게 할 수 있음  
  
GROUP BY도 중복값들을 제거해줬지만 이건 그룹화가 주된 목적이고  
DISTINCT는 말그대로 중복값들을 제거하는게 목적  
```sql
SELECT DISTINCT CategoryID
FROM Products;
-- GROUP BY는 결과가 정렬되서 나옴
-- DISTINCT는 그냥 DB에 나오는 순서대로 나옴. 즉 정렬하지 않고 정적으로 그냥 나옴
```

```sql
-- 오류 발생 예시, 집계함수와 함께 쓰이지 않으므로
SELECT COUNT DISTINCT CategoryID
FROM Products;
```

```sql
SELECT DISTINCT Country
FROM Customers
ORDER BY Country;
-- ORDER BY를 붙여서 수동으로 정렬을 하고 싶으면 해도 됨
```

#### 5) GROUP BY와 DISTINCT 함께 활용하기  
나라마다 겹치지 않는 CITY가 몇개가 들어 있는가
```sql
SELECT
  Country,
  COUNT(DISTINCT CITY)
FROM Customers
GROUP BY Country;
-- 만약 DISTINCT를 안쓰면 같은 CITY도 누적해서 세는거지만 이렇게 쓰면 같은 도시는 한번만 셈
```

>[!note] GROUP BY와 DISTINCT
> GROUP BY : 그룹화가 주 목적, 집계합수를 사용 가능, 결과가 정렬되서 출력됨  
> DISTINCT : 중복제거가 주 목적, 집계함수가 사용 불가능, 정렬을 하지 않으므로 더 빠름

___
## 쿼리 안에 서브쿼리

서브쿼리란 또 다른 쿼리 안에 들어있는 쿼리  
서브쿼리와 본쿼리 둘다 같은 테이블을 조작할 수도, 각각 다른 테이블을 조작할 수도 있음  
SELECT문 혹은 WHERE문에 서브쿼리를 넣어서 사용  

서브쿼리에는 비상관쿼리와 상관쿼리가 있음  

#### 1) 비상관쿼리
서브쿼리가 외부쿼리와 관련이 없이 독자적으로 돌아가는 것
```SQL
SELECT
  CategoryID, CategoryName, Description,
  (SELECT ProductName FROM Products WHERE ProductID = 1)
FROM Categories;
```

```SQL
SELECT * FROM Products
WHERE Price < (
  SELECT AVG(Price) FROM Products
);
```

```SQL
SELECT
  CategoryID, CategoryName, Description
FROM Categories
WHERE
  CategoryID =
  (SELECT CategoryID FROM Products
  WHERE ProductName = 'Chais');
```

```SQL
SELECT
  CategoryID, CategoryName, Description
FROM Categories
WHERE
  CategoryID IN
  (SELECT CategoryID FROM Products
  WHERE Price > 50);
```

#### 2) ALL / ANY 연산자  
~ ALL 서브쿼리의 모든 결과에 대해 ~하다  
~ ANY 서브쿼리의 하나 이상의 결과에 대해 ~하다
```SQL
SELECT * FROM Products
WHERE Price > ALL (
  SELECT Price FROM Products
  WHERE CategoryID = 2
);
-- 카테고리ID가 2인 제품들 가격들 모두보다 큰 가격이 조건이므로
-- 카테고리ID가 2인 제품 중 가장 비싼 제품보다 더 비싼걸 가져오는게 조건이 되는 것
```

```SQL
SELECT
  CategoryID, CategoryName, Description
FROM Categories
WHERE
  CategoryID = ANY
  (SELECT CategoryID FROM Products
  WHERE Price > 50);
-- 조건에 맞는 여러가지 중 어떤것 하나라도 = 이면 가져오는 것
-- 여기서 = ANY 를 IN으로 바꿔도 같은 의미가 되겠지
```

#### 3) 상관쿼리  
비상관쿼리는 서브쿼리와 본쿼리와 상관없이 독자적으로 돌아가던 것  
상관쿼리는 서브쿼리와 바깥쪽의 쿼리가 맞물려서 돌아감  
이건 뒤에서 배울 join으로 더 쉽게 가능하지만 서브쿼리로도 가능  
```SQL
SELECT
  ProductID, ProductName,
  (
    SELECT CategoryName FROM Categories C
    WHERE C.CategoryID = P.CategoryID
  ) AS CategoryName
FROM Products P;
-- Products는 P로, Categories는 C로 별명 만들어서 사용
-- C.CategoryID = P.CategoryID로 맞물려서 적용
```

```SQL
SELECT
  SupplierName, Country, City,
  (
    SELECT COUNT(*) FROM Customers C
    WHERE C.Country = S.Country
  ) AS CustomersInTheCountry,
  (
    SELECT COUNT(*) FROM Customers C
    WHERE C.Country = S.Country 
      AND C.City = S.City
  ) AS CustomersInTheCity
FROM Suppliers S;
```

```SQL
SELECT
  CategoryID, CategoryName,
  (
    SELECT MAX(Price) FROM Products P
    WHERE P.CategoryID = C.CategoryID
  ) AS MaximumPrice,
  (
    SELECT AVG(Price) FROM Products P
    WHERE P.CategoryID = C.CategoryID
  ) AS AveragePrice
FROM Categories C;
```

```SQL
SELECT
  ProductID, ProductName, CategoryID, Price
  -- ,(SELECT AVG(Price) FROM Products P2
  -- WHERE P2.CategoryID = P1.CategoryID)
FROM Products P1
WHERE Price < (
  SELECT AVG(Price) FROM Products P2
  WHERE P2.CategoryID = P1.CategoryID
);
```

#### 4) EXISTS / NOT EXISTS연산자  
존재하는가 존재하지 않는가
```SQL
SELECT
  CategoryID, CategoryName
  -- ,(SELECT MAX(P.Price) FROM Products P
  -- WHERE P.CategoryID = C.CategoryID
  -- ) AS MaxPrice
FROM Categories C
WHERE EXISTS (
  SELECT * FROM Products P
  WHERE P.CategoryID = C.CategoryID
  AND P.Price > 80
);
```

___
## JOIN

관계형 데이터베이스는 중복을 최소화하기 위해 정규화를 하는데  
그 분리된 테이블들을 다시 합쳐서 가져오는 과정을 JOIN으로 처리함  
```SQL
FROM Products P
JOIN Suppliers S
ON P.SupplierID = S.SupplierID
-- 위와 같은 모양새인데 이걸 

FROM (Products P JOIN Suppliers S) (ON P.SupplierID = S.SupplierID)
-- 이런 느낌으로 보는게 더 편함
-- P랑 S를 조인하는데 ON를 조건으로 합친다.
-- 두 테이블을 합친 그 테이블에서 select문 등을 수행한다. 라고 생각하면
-- 먼저 join한 후에 거기서 한다 생각하면 다 쉬움
```

#### 1) JOIN(INNER JOIN) - 내부 조인
양쪽 모두에 값이 있는 행만 반환함(즉 NULL이 발생하지 않게)  
INNER를 생략하고 JOIN으로 적으면 INNER JOIN임

```
SELECT * FROM Categories C
JOIN Products P 
  ON C.CategoryID = P.CategoryID; 
-- P에 있던 내용과 C에 있던 내용을 ID맞춰서 모두 가져옴
```

```
SELECT C.CategoryID, C.CategoryName, P.ProductName
FROM Categories C
JOIN Products P 
  ON C.CategoryID = P.CategoryID; 
-- ambiguous 주의!
-- ambiguous는 모호하다는 의미로 C.CategoriID가 아닌 그냥 CategoriID를 쓰면
-- P와 C 양쪽 테이블 모두 CategoriID를 가지고 있어서 어디껄 가져와야할지 모르겠다는 뜻임
-- 둘 중 어디껄 가져오던 상관없지만 컴퓨터는 구분 못하니 C.CategoriID로 C.을 써서 명시하는 것
```

```
SELECT
  CONCAT(
    P.ProductName, ' by ', S.SupplierName
  ) AS Product,
  S.Phone, P.Price
FROM Products P
JOIN Suppliers S
  ON P.SupplierID = S.SupplierID
WHERE Price > 50
ORDER BY ProductName;
```

```
-- 여러 테이블을 JOIN할 수 있음
SELECT 
  C.CategoryID, C.CategoryName, 
  P.ProductName, 
  O.OrderDate,
  D.Quantity
FROM Categories C
JOIN Products P 
  ON C.CategoryID = P.CategoryID
JOIN OrderDetails D
  ON P.ProductID = D.ProductID
JOIN Orders O
  ON O.OrderID = D.OrderID;
```

 JOIN한 테이블 GROUP하기

```
SELECT 
  C.CategoryName,
  MIN(O.OrderDate) AS FirstOrder,
  MAX(O.OrderDate) AS LastOrder,
  SUM(D.Quantity) AS TotalQuantity
FROM Categories C
JOIN Products P 
  ON C.CategoryID = P.CategoryID
JOIN OrderDetails D
  ON P.ProductID = D.ProductID
JOIN Orders O
  ON O.OrderID = D.OrderID
GROUP BY C.CategoryID;
```

```
SELECT 
  C.CategoryName, P.ProductName,
  MIN(O.OrderDate) AS FirstOrder,
  MAX(O.OrderDate) AS LastOrder,
  SUM(D.Quantity) AS TotalQuantity
FROM Categories C
JOIN Products P 
  ON C.CategoryID = P.CategoryID
JOIN OrderDetails D
  ON P.ProductID = D.ProductID
JOIN Orders O
  ON O.OrderID = D.OrderID
GROUP BY C.CategoryID, P.ProductID;
```

  
같은 테이블끼리 SELF JOIN도 가능

```
SELECT
  E1.EmployeeID, CONCAT_WS(' ', E1.FirstName, E1.LastName) AS Employee,
  E2.EmployeeID, CONCAT_WS(' ', E2.FirstName, E2.LastName) AS NextEmployee
FROM Employees E1 JOIN Employees E2
ON E1.EmployeeID + 1 = E2.EmployeeID;
-- 1번의 전, 마지막 번호의 다음은?
-- 예를들어 1번은 전임자가 없고, 9번은 후임자가 없음
-- 그러니 테이블을 조인했을때 가져올 값이 없음
-- 이런식으로 가져올 값이 없을때 그냥 안가져오는게 inner join
```

2) LEFT / RIGHT OUTER JOIN - 외부조인  
반대쪽에 데이터가 있든 없든(NULL) 선택된 방향에 있기만 하면 출력  
왼쪽만 데이터가 있다면 오른쪽을 비우더라도 데이터를 가져옴, 빈 칸은 NULL값  
OUTER는 생략해도 됨, LEFT JOIN이나 RIGHT JOIN만 쓰면 됨

```
SELECT
  E1.EmployeeID, CONCAT_WS(' ', E1.FirstName, E1.LastName) AS Employee,
  E2.EmployeeID, CONCAT_WS(' ', E2.FirstName, E2.LastName) AS NextEmployee
FROM Employees E1
LEFT JOIN Employees E2
ON E1.EmployeeID + 1 = E2.EmployeeID
ORDER BY E1.EmployeeID;
-- LEFT를 RIGHT로 바꿔서도 실행해 보기
```

```
SELECT
  C.CustomerName, S.SupplierName,
  C.City, C.Country
FROM Customers C
LEFT JOIN Suppliers S
ON C.City = S.City AND C.Country = S.Country;
-- LEFT를 RIGHT로 바꿔서도 실행해 보기
```

```
SELECT
  IFNULL(C.CustomerName, '-- NO CUSTOMER --'),
  IFNULL(S.SupplierName, '-- NO SUPPLIER --'),
  IFNULL(C.City, S.City),
  IFNULL(C.Country, S.Country)
FROM Customers C
LEFT JOIN Suppliers S
ON C.City = S.City AND C.Country = S.Country;

-- LEFT를 RIGHT로 바꿔서도 실행해 보기
```

  
3) CROSS JOIN - 교차 조인  
조건 없이 모든 조합 반환(A * B)

```
SELECT
  E1.LastName, E2.FirstName
FROM Employees E1
CROSS JOIN Employees E2
ORDER BY E1.EmployeeID;
-- 실전에서 뭐 쓸일은 없겠지만 이런 기능도 가능하다
```