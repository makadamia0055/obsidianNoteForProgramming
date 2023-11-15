---
aliases:
---
---
![[격리수준(Isolation level)]]
# MySQL의 격리 수준(MySQL에만 해당하는 요소들만)
- MySQL 에서는 격리 수준이 높아지더라도, Serializable 수준이 아니라면 크게 성능의 개선이나 저하는 발생하지 않는다. 
![[MySQL의 격리수준과 이상 현상.png]]
- InnoDB에서는 독특한 특성 때문에 Repeatable reads 격리 수준에서도 Phantom read가 발생하지 않는다. 
- 일반적인 온라인 서비스 용도의 데이터 베이스는 Read committed와 Repeatable read 중 하나를 사용한다. 
	- 오라클 같은 DBMS에서는 주로 Read committed 수준을 많이 사용
	- MySQL에서는 Repeatable read를 주로 사용.
- 아래 예시들은 쿼리 로직과 Commit 시점이 달라야 구현될 수 있기에, Autocommit이 off인 상태에서 테스트 할 수 있다. 

# READ UNCOMMITTED
- 상기 격리 수준의 Read uncommitted 참조
- Read uncommitted는 Dirty reads 유발로, RDBMS 표준에서는 트랜잭션 격리 수준으로 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이다. 
- MySQL에서는 최소한 Read Committed 이상의 격리 수준을 사용할 것을 권한다. 


---

# READ COMMITTED
- 오라클 DBMS의 기본 격리 수준이며, 온라인 서비스에서 가장 많이 선택되는 격리수준.
- Dirty reads 방지가 방지됨., Non-repeatable 부정합 발생 가능.(Phantom reads도)
![[MySQL의 Read committed 격리 수준 구현 예시.png]]
- MySQL에서는 [[언두 로그]]를 통한 [[MVCC]]로 Read committed 격리 수준을 구현하고 있다. 
	- 사용자 A의 트랜잭션에서 employees 테이블 값을 업데이트 하면, 해당 값은 테이블에 즉시 기록되고, 이전 값인 'Lara'는 언두 영역으로 백업된다. 
	- 트랜잭션 A가 커밋 되기 전에 다른 트랜잭션 B에서 해당 칼럼의 값을 조회하면, 현 테이블의 'Toto'를 조회하는 것이 아니라, 언두 영역에 백업된 레코드에서 'Lara'를 가져온다. 
- 그러나 Non-repeatable reads 부정합은 발생한다. 
	- 해당 예시는 생략.
	- Non-repeatable reads 부정합 현상은 일반적 웹 프로그램에서는 크게 문제되지 않을 수 있지만, 하나의 트랜잭션에서 동일 데이터를 여러 번 읽고 변경하는 작업이 금전적인 처리와 연결되면 문제가 될 수도 있다. 

>[!warning] 트랜잭션 내에서 실행되는 SELECT문과 트랜잭션 없이 실행되는 SELECT문의 차이
>격리 수준에 따라 트랜잭션 내에 실행되는 SELECT와 트랜잭션 없이 실행되는 SELECT문의 차이가 발생할 수 있다.
>Read committed 이하의 트랜잭션에서는 트랜잭션 내부, 외부 SELECT 문장의 차이가 별로 없지만, Repeatable read 수준 이상에서는 동작에 차이가 있다. 

### cf) 그럼 트랜잭션 없는 SELECT문은 어떻게 동작하는가?
- [트랜잭션을 사용하지 않은 SELECT문이 가져오는 값은 데이터베이스의 격리 수준에 따라 달라집니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16).
- [대부분의 데이터베이스 시스템에서 기본적으로 트랜잭션을 사용하지 않은 SELECT문은 **커밋된 데이터만** 읽습니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16). [이는 **READ COMMITTED** 격리 수준에서의 기본 동작입니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16). [즉, SELECT문은 다른 트랜잭션에서 아직 커밋되지 않은 변경 사항을 볼 수 없습니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16).
- [그러나 일부 데이터베이스 시스템에서는 **READ UNCOMMITTED** 격리 수준을 사용하여 트랜잭션을 사용하지 않은 SELECT문이 커밋되지 않은 데이터를 읽을 수 있게 할 수 있습니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16). [이는 일반적으로 권장되지 않는 방식입니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16). [왜냐하면 이렇게 하면 "더티 읽기(Dirty Read)"와 같은 문제가 발생할 수 있기 때문입니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16). [더티 읽기는 한 트랜잭션에서 변경한 데이터를 다른 트랜잭션이 읽었는데, 첫 번째 트랜잭션이 롤백되어 변경이 취소된 경우를 말합니다](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[1](https://velog.io/@jurlring/TransactionalreadOnly-true%EC%97%90%EC%84%9C-readOnly-true%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%97%AD%ED%95%A0%EC%9D%B4%EA%B3%A0-%EA%BC%AD-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C)[2](https://learn.microsoft.com/ko-kr/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide?view=sql-server-ver16).

---
# REPEATABLE READ
- MySQL의 [[InnoDB]]에서 기본으로 사용하는 격리 수준.
	- [[바이너리 로그]]를 가진 MySQL 서버에서는 최소 Repeatable read 격리 수준 이상을 사용해야 한다. (제한 사항인 듯)[출처]([[MySQL] 복제 구성을 위한 바이너리 로깅 형식 (tistory.com)](https://omty.tistory.com/63))
	- 바이너리 로그가 트랜잭션의 변경사항을 기록하기 때문에 Repeatable read 미만의 격리 수준에서는 데이터 불일치가 발생할 수 있다.
- InnoDB에서는 Repeatable read 격리 수준도 언두 로그를 이용한 MVCC로 구현된다.
	- Read committed 격리 수준도 언두 로그을 이용한 MVCC로 구현되는데, Repeatable read 격리수준과의 차이는 언두 영역에 백업된 레코드의 여러 버전 가운데 몇 번째 이전 버전까지 찾아 들어가야 하느냐에 있다. 
	- InnoDB의 모든 트랜잭션은 고유한 트랜잭션 번호를 가지며, 언두 영역에 백업된 모든 레코드에는 변경을 발생시킨 트랜잭션의 번호가 포함되어 있다. 
		- 그리고 언두 영역의 백업된 데이터는 InnoDB 스토리지 엔진이 불필요하다고 판단하는 시점에 주기적으로 삭제된다.
		- Repeatable read 격리 수준에서는 [[MVCC]]를 보장하기 위해 실행 중인 트랜잭션 중에 가장 오래된 트랜잭션 번호보다 트랜잭션 번호가 앞선 언두 영역의 데어터는 삭제할 수가 없다. 
		- 그렇다고 가장 오래된 트랜잭션 번호 이전의 트랜잭션에 의해 변경된 모든 언두 데이터가 필요한 것은 아니다. 더 정확하게는 특정 트랜잭션 번호의 구간 내에서 백업된 언두 데이터가 보존돼야 한다. 

![[MySQL의 Repeatable 격리 수준 예시.png]]

- 위 예제를 살펴보면 사용자 A의 트랜잭션 번호는 12이고, 사용자 B의 트랜잭션 번호는 10이다. 이때 사용자 A가 레코드를 변경했더라도 사용자 B는 자신보다 작은 트랜잭션 번호(트랜잭션 10보다 낮은 트랜잭션)의 변경 사항만 보게 된다. 
	- 이런 구조 때문에 한 트랜잭션이 장시간 종료되지 않으면, 언두 영역이 백업된 데이터로 무한정 커질 수 있다. 
	- 그렇게 되면 MySQL 서버의 처리 성능이 떨어질 수 있다.
- Repeatable read 격리수준에서도 Phantom reads 부정합이 발생할 수 있다. 
	- 예시 그림만
![[MySQL에서 Repeatable read의 Phantom reads 현상 예시.png]]
- SELECT ... FOR UPDATE 쿼리는 SELECT하는 레코드에 쓰기 잠금을 걸어야 하는데, 언두 레코드에는 잠금을 걸 수 없다.
	- 그래서 SELECT ... FOR UPDATE나 SELECT ... LOCK IN SHARE MODE로 조회되는 레코드는 언두 영역의 변경 전 데이터를 가져오는 것이 아니라 현재 레코드의 값을 가져오게 되는 것이다. 

---
# SERIALIZABLE
- 가장 단순한 격리 수준이면서도 동시에 가장 엄격한 격리 수준이다. 
	- 동시 처리 성능도 다른 트랜잭션 격리 수준보다 떨어진다. 
- InnoDB 테이블에서 기본적으로 순수한 SELECT 작업(INSERT ... SELECT ... 또는 CREATE TABLE ... AS SELECT ... 가 아닌)은 아무런 레코드 잠금도 설정하지 않고 실행된다. 
	- 이것이 "Non-locking consistent read(잠금이 필요 없는 일관된 읽기)"이다.
- 하지만 트랜잭션이 SERIALIZABLE가되면 읽기 작업도 공유 잠금(읽기 잠금)을 획득해야만 하며, 동시에 다른 트랜잭션은 그러한 레코드를 변경하지 못하게 된다. 
	- 즉, 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없는 것이다. 
	- 때문에 SERIALIZABLE 격리 수준에서는 Phantom reads 현상이 발생하지 않는다.
	- 하지만 InnoDB 스토리지 엔진에서는 갭 락과 넥스트 키 락 덕분에 Repeatable read 격리 수준에서도 이미 Phantom reads가 발생하지 않기 때문에, 굳이 Serializable를 사용할 필요성은 없어 보인다.
		- 갭 락과 넥스트 키 락은 격리 수준에 무관하게 작동하는 락.
		- 물론 Repeatable read 격리 수준에서 앞에서 설명한  SELECT ... FOR UPDATE나 SELECT ... LOCK IN SHARE MODE쿼리의 경우 Phantom read 현상이 발생할 수 있다. 
---

## 참조 문헌
[공식 문서](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)