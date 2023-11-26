2) DML (데이터 조작 언어, Data Manipulation Language)

- 데이터베이스 검색 및 변경등을 위한 언어

- 일반 프로그램언어(C, Java 등)에 포함되어 사용하거나 별도로 명령어 입력하여 사용

- DML을 프로그램과 연동하는 방법은, 그냥 코드에 DML작성 후 전처리기로 변환하거나, 해당 언어(ODBC, JDBC)로 직접 관련함수를 호출할 수 있음

- 단위 : row단위(가로줄, 행)

- DML만 commit/rollback 가능, 다른 언어들은 commit/rollback 불가능

- INSERT : 데이터 추가

- DELETE : 데이터 삭제

- UPDATE : 데이터 수정

- DQL + DML = CRUD