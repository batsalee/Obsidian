# DB

#### 데이터베이스 이론
[[데이터베이스 이론]]

#### 데이터베이스 기본문법
[[SQL기본문법]]

#### MySQL / MySQL Workbench 설치 및 기본 사용법
https://smallpants.tistory.com/92

#### C++과 MySQL 연동
[[MySQL 연동]]

#### SQL의 종류
SQL(Structured Query Language) : 구조화된 질의 언어
1. [[DQL]] : 데이터 질의 언어(SELECT), SELECT는 DQL로 따로 치기도 하는데 대부분은 그냥 DML에 포함시킴	
2. [[DML]] : 데이터 검색 및 변경 언어(INSERT, UPDATE, DELETE)
3. [[DDL]] : DBA가 DB를 만들때 개념적 스키마를 명시하기 위해 사용하는 언어(CREATE, ALTER, DROP 등등)
4. [[DCL]] : 데이터 접근 권한제어 언어(GRANT, REVOKE)
5. [[TCL]] : 트랜잭션 언어(COMMIT, ROLLBACK)

#### CRUD 요약

| SELECT            | INSERT                                                        | UPDATE                                                        | DELETE            |
|:----------------- |:------------------------------------------------------------- |:------------------------------------------------------------- |:----------------- |
| SELECT FROM WHERE | INSERT INTO VALUES                                            | UPDATE SET WHERE                                              | DELETE FROM WHERE |
|                   | 도메인, 키, 엔터티무결성, 참조무결성 위반 가능 | 도메인, 키, 엔터티무결성, 참조무결성 위반 가능 |참조무결성 위반 가능|

> [!WARNING]
> UPDATE와 DELETE에는 WHERE 안붙이면 모든 행에 적용되므로 주의