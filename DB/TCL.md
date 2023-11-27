# TCL 

트랜잭션 언어(Transaction Control Language)

==일괄처리, INSERT, UPDATE, DELETE에서만== 사용 가능하며 한번 커밋하면 롤백 불가능
- COMMIT : 정상적으로 저장
- ROLLBACK : 취소

___
## 트랜잭션이란?

대부분의 데이터베이스는 개발자가 실수로 잘못된 명령문을 입력했을 경우  
다시 원래 상태로 되돌리기 위해 명령에 따른 데이터 변경을 물리적인 하드디스크에 즉시 반영하지 않음  

따라서 개발자가 commit을 하기 전까지 입력한 명령문들은 메모리에서만 동작하고  
물리적인 하드디스크에는 commit하는 시점에 반영됨  

개발자의 데이터 작업 시작시점부터 커밋하기까지를 한 덩어리로 취급하며  
이 한덩어리(데이터 처리들)를 트랜잭션이라고 부름

___
## 자동 커밋 기능 해제하기

MySQL Workbench는 기본적으로 자동커밋기능이 활성화되어있음  
즉 COMMIT과 ROLLBACK기능을 활용하려면 해당 기능을 off해줘야 함  
![[Pasted image 20231127122447.png|800x500]]
Preferences -> SQL Execution -> New connections use auto commit mode 체크해제 -> 재접속  
완료하면 자동커밋기능이 꺼지고 직접 커밋을 해줘야만 적용되게 설정됨  
이제부턴 DML을 하드디스크에 반영하려면 COMMIT; 쿼리를 입력해줘야 함  

> [!warning]
> 다만 DML만 자동 커밋되지 않게 해주는 것이고
>  TRUNCATE같은건 바로 하드디스크에 적용되므로 주의해야함

___
## ROLLBACK

```SQL
-- 데이터 삭제하기 
delete from test_table1;

-- 롤백하여 되돌리기
rollback;
select * from test_table1;

-- 잘 되돌아 오는걸 확인할 수 있음
```
DML인 INSERT / DELETE / UPDATE는 ROLLBACK이 가능하지만  
TRUNCATE등은 바로 데이터베이스에 반영되므로 ROLLBACK이 불가능함  

___
## COMMIT

하나의 트랜잭션을 물리적인 데이터베이스에 적용하는 작업  
COMMIT을 하면 ROLLBACK을 해도 되돌릴 수 없음  
```sql
-- 커밋하고 롤백으로 되돌려보기
delete from test_table1;
commit;
select * from test_table1;

rollback;
select * from test_table1; -- 데이터가 복구되지 않는다.
```
다만 savepoint를 지정해뒀다면 commit 했어도 rollback이 가능함

___
## SavePoint

SavePoint를 지정해뒀다면 commit을 했더라도 지정된 위치로 rollback할 수 있음
```sql
-- SavePoint 활용하기
savepoint aa; -- savepoint를 지정하고
commit; -- commit을 하고
delete from test_table1; -- 데이터를 삭제해도
rollback to aa; -- savepoint 덕분에 다시 데이터가 돌아온다
```



※ 참고문헌
[https://jminie.tistory.com/32](https://jminie.tistory.com/32)