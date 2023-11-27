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
	프로그램에서 CREATE, READ, UPDATE, DELETE를 수행하는 기능들의 약자  

>[!note]
> DDL은 column단위(세로줄)을 변경
> DML은 row단위(가로줄)을 변경

