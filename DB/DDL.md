# DDL

데이터 정의 언어(Data Definition Language) 혹은 스키마 언어로 불림  
DBA가 DB를 만들때 column단위(세로줄, 열)의 개념적 스키마를 명시하기 위해 사용하는 언어  
- CREATE : 생성
- ALTER : 수정(MODIFY), 삭제(DROP), 추가(ADD)
- DROP : 테이블의 구조 자체를 삭제
- TRUNCATE : 테이블의 구조는 남겨두고 데이터만 전부 삭제
- RENAME : 테이블명 혹은 칼럼명 변경

___
## CREATE
#### 1) Create Schema
- 데이터베이스를 만드는 개념(Create Database와 같음)
- 테이블, 제약조건, 뷰, 도메인, 이벤트, 함수, 색인, 트리거 등을 구조체로 묶은것을 만드는 것
- `Create Schema Company Authorization JSmith` 이러면 Company에 테이블, 뷰, 도메인 등등을 생성

#### 2) CREATE TABLE - 테이블 생성
- 테이블 이름과 함께 새로운 테이블 생성
- 애트리뷰트와 데이터 유형(도메인 제약조건이기도 함), 제약조건을 기술
- 기본키(Primary key), 보조키(unique), 외래키도 표시

##### 2-1) SQL로 생성
```SQL
CREATE TABLE people (
  person_id INT,
  person_name VARCHAR(10),
  age TINYINT,
  birthday DATE
);

-- INT는 정수
-- VARCHAR(10)은 문자열 10글자까지
-- TINYINT는 사람 나이는 수백 수천까지 안가므로 좀 작은 정수형
-- DATE는 날짜 자료형
```
해당 테이블을 만들 DB를 더블클릭 하고 SQL File에 위 구문을 입력한 후 번개모양 누르고 refresh  

##### 2-2) GUI로 생성
혹은 아래처럼 해당 DB에 우클릭하면 Create TABLE이 있는데 이걸 이용해서 GUI로 생성도 가능  
GUI로 생성하면 더 세부적인 제약사항이나 따옴표같은것도 저절로 써줘서 실수의 여지를 줄이기 좋음  
![[Pasted image 20231127120006.png|800x500]]
빨간 사각형처럼 다 써준 후 Apply 누르면  
![[Pasted image 20231127120052.png|800x500]]
이렇게 세부적인 사항까지 적어줘서 실수의 여지도 적고 더 편함  

#### 3) 테이블 생성시 제약 넣기

##### 3-1) NOT NULL, UNIQUE 등의 제약설정
```SQL
CREATE TABLE people (
  person_id INT AUTO_INCREMENT PRIMARY KEY,
  person_name VARCHAR(10) NOT NULL,
  nickname VARCHAR(10) UNIQUE NOT NULL,
  age TINYINT UNSIGNED,
  is_married TINYINT DEFAULT 0
);
```

##### 3-2) 도메인 제약조건 만드는 법
```SQL
Create Domain SSN_TYPE AS Char(9);
Create Domain DNum AS Int Check(DNumber>0 AND DNumber<21);
```

| 제약           | 설명                                                                                 |
| -------------- | ------------------------------------------------------------------------------------ |
| AUTO_INCREMENT | 새 행 생성시마다 자동으로 1씩 증가, 이 속성을 적용했으면 INSERT할때 값 직접 안넣어줌 |
| PRIMARY KEY    | 중복 입력 불가, NULL(빈 값) 불가                                                     |
| UNIQUE         | 중복 입력 불가                                                                       |
| NOT NULL       | NULL(빈 값) 입력 불가                                                                |
| UNSIGNED       | (숫자일시) 양수만 가능                                                               |
| DEFAULT        | 값 입력이 없을 시 기본값, DEFAULT <값> 으로 지정                                     |
| CHECK          | 값을 특정한 값으로 한정하려면 CHECK(값의 범위)                                       |

PRIMARY KEY는 기본키로 테이블마다 하나만 가능

- 기본적으로 인덱스 생성(기본키 행 기준으로 빠른 검색 가능)

- 보통 AUTO_INCREMENT와 함께 사용

- 각 행을 고유하게 식별 가능하게 만드므로 테이블마다 하나씩 둬야 함
##### 3-3) 참조무결성 설정
```SQL
Create Table Employee (
  FName VARCHAR(30) NOT NULL,
  MInit CHAR,
  LName VARCHAR(30) NOT NULL,
  SSN CHAR(9),
  BDate DATE,
  DNo INTEGER DEFAULT 1,
  SuperSSN CHAR(9),
  Primary Key (SSN),
  Foreign Key (DNo) References Department(DNumber)
                   On Delete Set Default On Update Cascade,
  Foreign Key (SuperSSN) References Employee(SSN)
  On Delete Set Null On Update Cascade
);
```
Foreign Key에 제약을 걸때 Delete와 Update때 Restrict, Cascade, Set Null, Set Default 중 선택적으로 적용
