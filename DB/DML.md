# DML

데이터 조작 언어(Data Manipulation Language)
데이터베이스에 row단위(가로줄, 행)로 검색 및 변경등을 위한 언어  

일반 프로그램언어(C, Java 등)에 포함되어 사용하거나 별도로 명령어 입력하여 사용하며  
DML을 프로그램과 연동하는 방법은 그냥 코드에 DML작성 후 전처리기로 변환하거나  
해당 언어(ODBC, JDBC)로 직접 관련함수를 호출할 수 있음  

DML만 commit/rollback 가능하며 다른 언어들은 commit/rollback이 불가능  
- INSERT : 데이터 추가
- UPDATE : 데이터 수정
- DELETE : 데이터 삭제
- SELECT는 DQL로 따로 치기도 하는데 대부분은 그냥 DML에 포함시킴  

DQL과 DML을 합쳐 CRUD라고 부름  
	DB에서 많이 수행하는 4가지 작업인 CREATE, READ, UPDATE, DELETE의 약자  
	CRUD만 익혀도 대부분의 프로그램은 만들 수 있음

>[!note]
> DDL은 column단위(세로줄)을 변경
> DML은 row단위(가로줄)을 변경

___
## SELECT

SELECT는 아래 글에서 기본 SQL 문법과 함께 정리해뒀음
[[DQL과 DB기본문법]]

___
## INSERT

INSERT INTO - 데이터 삽입  
스키마도 만들고 테이블도 만들었다면 이제 데이터를 넣을 차례  

>[!warning]
> 도메인, 키, 엔터티무결성, 참조무결성 4가지 제약조건 위반 가능

```sql
INSERT INTO people
  (person_id, person_name, age, birthday)
  VALUES (1, '홍길동', 21, '2000-01-31');
```

```sql
-- 모든 컬럼에 값 넣을 때는 컬럼명들 생략 가능
INSERT INTO people
  VALUES (2, '전우치', 18, '2003-05-12');
```

```sql
-- 일부 컬럼에만 값 넣기 가능 (NOT NULL은 생략 불가)
INSERT INTO people
  (person_id, person_name, birthday)
  VALUES (3, '임꺽정', '1995-11-04');
```

```sql
-- 자료형에 맞지 않는 값은 오류 발생
INSERT INTO people
  (person_id, person_name, age, birthday)
  VALUES (1, '임꺽정', '스물여섯', '1995-11-04');
```

```sql
-- 여러 행을 한 번에 입력 가능
INSERT INTO people
  (person_id, person_name, age, birthday)
  VALUES 
    (4, '존 스미스', 30, '1991-03-01'),
    (5, '루피 D. 몽키', 15, '2006-12-07'),
    (6, '황비홍', 24, '1997-10-30');
```

GUI로는 아래처럼 SELECT문의 결과 아래에 값을 추가로 적고 Apply를 누르면 insert문 자동 작성 됨  
![[Pasted image 20231127124329.png|800x600]]  
만약 CREATE할때 제약을 걸어뒀다면 잘못된 값을 입력시도하면 오류가 나면서 INSERT 거절됨  

___
## UPDATE

UPDATE - 주어진 조건의 행 수정하기

>[!warning]
> 도메인, 키, 엔터티무결성, 참조무결성 4가지 제약조건 위반 가능
> 삭제 후 삽입연산과 동일하므로 해당 상황에서 발생하는 제약조건 위반 가능

```sql
UPDATE menus
SET menu_name = '삼선짜장'
WHERE menu_id = 12;

-- 테이블명은 menus
-- 애트리뷰트명은 menu_name
-- 조건은 menu_id = 12
-- 즉 menus테이블에서 menu_id가 12인 튜플의 menu_name값을 삼선짜장으로 바꾼다
```

```sql
-- 여러 칼럼 수정하기

UPDATE menus
SET 
  menu_name = '열정떡볶이',
  kilocalories = 492.78,
  price = 5000
WHERE 
  fk_business_id = 4
  AND menu_name = '국물떡볶이';
```

#### 컬럼 데이터 활용해서 수정하기

```sql
UPDATE menus
SET price = price + 1000
WHERE fk_business_id = 8;
```

```sql
UPDATE menus
SET menu_name = CONCAT('전통 ', menu_name)
WHERE fk_business_id IN (
  SELECT business_id 
  FROM sections S
  LEFT JOIN businesses B
    ON S.section_id = B.fk_section_id 
  WHERE section_name = '한식'
);
```

>[!warning]
> UPDATE문에 WHERE문을 안붙이면 모든 행이 값이 변해버림
> ```
> UPDATE menus
> SET menu_name = '획일화';
> ```
> DELETE와 UPDATE는 WHERE문 안붙이면 사고 날수 있으니 꼭 붙이는거 신경쓰기

___
## DELETE

DELETE - 주어진 조건의 행 삭제하기

>[!warning]
> 참조무결성 위반 가능하며 위반시 아래의 조치를 취할 수 있음
>  - RESTRICT(연산 거절)
>  - CASCADE(참조하는 모든 튜플 연쇄적으로 삭제)
>  - SET NULL(참조하는 외래키를 NULL로 설정)
> 단, CASCADE가 참조무결성 조건에 명시되면 여러 튜플이 삭제될 가능성이 있으니 주의

#### MySQL Workbench의 DELETE 안전장치 해제
MySQL Workbench에서 DELETE를 사용하려는 경우 안전장치가 걸려있기 때문에 먼저 설정을 변경해줘야 함  
※ Preferences > SQL Editor > Safe Updates 항목 체크오프하고 다시 접속  
Preferences는 아래 사진처럼 Workbench 오른쪽위에 있는 해당 아이콘임  
![[Pasted image 20231127125429.png]]

다시 접속은 다 껐다 킬 필요 없이 아래에 mydb 옆의 x만 눌렀다가 다기 홈에서 클릭해서 들어오면 됨  
![[Pasted image 20231127125449.png]]

#### DELETE문 예시
```sql
DELETE FROM businesses
WHERE status = 'CLS';
-- 매우 주의해야할 것은 WHERE문 빼먹고 안쓰면 테이블 다 날릴 수 있음
-- WHERE문을 안쓰면 DROP businesses와 같게 됨
```

>[!warning]
> DELETE문에 WHERE문을 안붙이면 모든 값이 다 삭제된다.
> ```
> DELETE FROM businesses;
> -- 이런식으로 테이블 다 날리지 않게 주의하기
> ```
> DELETE와 UPDATE는 WHERE문 안붙이면 사고 날수 있으니 꼭 붙이는거 신경쓰기

```sql
-- 만약 id가 18이었던 화룡각, 19였던 철구분식, 20이었던 얄코렐라를 DELETE한 후에
-- 아래처럼 다시 INSERT하는 경우 각 튜플의 id가 원래 값인 18, 19, 20이 들어감
-- 즉 DELETE는 지워도 값을 기억하고 있다는 것

INSERT INTO businesses (fk_section_id, business_name, status, can_takeout)
VALUES  (3, '화룡각', 'OPN', 1),
        (2, '철구분식', 'OPN', 1),
        (5, '얄코렐라', 'RMD', 1);
```
반면 TRUNCATE문은 아예 테이블을 삭제했다가 다시 만드는 초기화의 개념이므로 삭제 전의 값을 기억하지 않음  

즉 테이블 정보를 아예 초기화할땐 DELETE가 아니라 TRUNCATE를 사용(소수점 자르는 함수 TRUNCATE와 이름만 같고 다른 기능임)  
```sql
TRUNCATE businesses;
```

```sql
-- TRUNCATE로 데이터를 다 날리고 아래처럼 추가를 하면
-- DELETE와 달리 id가 1, 2, 3으로 시작됨
-- 즉 완전 전체 초기화를 하려면 TRUNCATE를 쓰면 된다

INSERT INTO businesses (fk_section_id, business_name, status, can_takeout)
VALUES  (3, '화룡각', 'OPN', 1),
        (2, '철구분식', 'OPN', 1),
        (5, '얄코렐라', 'RMD', 1);
```

___

## 요약





※ 참고 문헌

얄코님 MySQL강의 무료파트
[https://www.youtube.com/watch?v=dgpBXNa9vJc](https://www.youtube.com/watch?v=dgpBXNa9vJc) 
[https://www.yalco.kr/lectures/sql/](https://www.yalco.kr/lectures/sql/)