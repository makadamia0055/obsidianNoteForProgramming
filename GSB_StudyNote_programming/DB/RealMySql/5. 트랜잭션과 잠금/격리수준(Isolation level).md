---
tags:
---
---
# 트랜잭션의 격리 수준(Isolation level)이란?
- 여러 트랜잭션이 동시에 처리될 때 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 말지를 결정하는 것.
- Isolation은 ACID의 I에 해당한다. 
- 일반적으로 데이터의 일관성(Consistency)과 동시성(Concurrency)은 trade-off 관계, 즉 상충 관계에 놓여있다. 
	- 데이터 일관성을 지키기 위해서는 동시성을 다소 희생할 수 밖에 없고, 그 반대의 경우도 마찬가지다. 
	- 트랜잭션의 격리 수준이란 이렇게 데이터 일관성과 동시성의 상충 관계 속에서, 성능 및 신뢰성, 일관성, 결과의 재현성을 고려하여, 고안된 정책 설정 수준 단위라고 볼 수 있다.  
		- 즉, 트랜잭션의 격리 수준은 트랜잭션 레벨의 [[동시성 제어]] 정책인 셈이다. 
- 트랜잭션의 격리 수준은 SQL:1992 표준에 따라 크게 아래 네 단계로 나뉜다.
	- READ UNCOMMITTED
	- READ COMMITTED
	- REPEATABLE READ
	- SERIALIZABLE
- 4개의 격리 수준에서 뒤로 갈수록 각 트랜잭션 간의 데이터 격리(고립) 정도가 높아지며, 동시처리 성능도 떨어지는 것이 일반적이다. 
- 또, 트랜잭션의 격리 수준이 낮을 수록 각 단계마다 **읽기 이상 현상**(Read Phenomena)이 나타날 수 있다.
![[격리 수준 별 나타날 수 있는 읽기 이상현상.png]]

---
# 읽기 이상 현상(Read Phenomena)
- 읽기 이상 현상은 한 트랜잭션이 업데이트 하고 있는 데이터를 다른 트랜잭션이 접근하는 순간에 읽어날 수 있는 이상 현상으로 ANSI/ISO 표준 SQL 92에 세 가지가 제시되어 있다.
	- Dirty Reads
	- Non-repeatable reads
	- Phantom reads
- 부정합이라고도 한다.


## Dirty reads
 - Dirty reads 현상은 한 트랜잭션에서 업데이트 했지만, 아직 커밋되지 않은 데이터에 다른 트랜잭션이 접근했을 때 일어날 수 있는 읽기 이상현상이다.
 - uncommitted dependency라고도 불린다. 

![[dirty read example.png]]

- 상기 예시에서 트랜잭션 2가 id == 1인 값을 업데이트 하고 나서, commit 하지 않았는데, 해당 **수정 중인(반영되지 않은)** 데이터에 트랜잭션 1이 접근해 읽는 현상이 **Dirty Reads** 현상이다.  
- 예시처럼 트랜잭션 1에서 확정되지 않은 값을 가져갔기에, 트랜잭션 2에서 롤백을 한다던지, 다시 새로운 값으로 업데이트 하는 등 데이터 일관성이 깨질 수 있다.
- 이러한 Dirty reads 현상을 방지하기 위해서 스케쥴 직렬화가 요구된다. [출처](https://www.geeksforgeeks.org/dbms-dirty-read-in-sql/)
- 이러한 Dirty reads 현상은 Read Uncommitted 격리 수준에서만 발생한다.

## Non-repeatable reads
- 중간에 실행된 다른 트랜잭션의 값 변경으로 인해, 먼저 시작된 트랜잭션 단위 안에서 특정 row에 대한 접근을 여러 번 수행했을 때 그 값이 반복 불가능하게(=일정하지 않게) 읽혀지는 현상.
![[NonRepeatable reads 예시.png]]
- 상기 예시처럼 먼저 시작한 트랜잭션 1이 id == 1 인 row를 읽고, 나중에 시작된 트랜잭션 2가 해당 값을 업데이트를 하면서, id == 1 인 데이터를 트랜잭션 1에서 다시 읽었음에도, 해당 데이터가 반복되지 않고, 다른 값으로 리턴된다. 즉, 한 트랜잭션(트랜잭션 1) 내부에서 같은 read를 했는데 반복되지 않는다.
- 한 트랜잭션이 읽고 있는 값을 변경할 수 있기 때문에 Non-repeatable reads가 일어난다.

## Phantom reads
- Phantom reads는 한 트랜잭션 단위 안에서 조건에 맞는 여러 행이나 레코드를 읽을 때 발생할 수 있는 이상 읽기 현상.
- 한 트랜잭션이 동일한 쿼리를 실행할 때, 두번의 쿼리 실행 사이에 다른 트랜잭션이 행을 삽입, 갱신, 삭제할 때 발생한다. 
![[Phantom reads 현상 예시.png]]

- 위의 예시에서처럼,  트랜잭션 1에서 동일한 쿼리로 특정 범위의 열들에 대한 검색을 실행하였는데, 중간에 끼어든 다른 트랜잭션의 값 삽입으로, 같은 트랜잭션 단위에서 동일 쿼리로 다른 결과가 도출되게 된다.

---
 ### 두 가지의 이상 읽기 현상 방지 전략
 - Non-repeatable reads와 Phantom reads를 방지하기 위한 두 가지 전략이 있다.
	 - 잠금 기반 동시성 제어(Lock-based concurrency control)
		 - 잠금을 통해 직렬 스케줄을 생성하여 **방지**.
	 - 다중 버전 동시성 제어(Multiversion concurrency control, MVCC)
		 - 트랜잭션 시작 시 생성된 스냅 샷을 기반으로 이상 읽기 현상 **탐지**.
>[!note]
> 격리 수준은 하나의 표준 정의이고, 그 표준 정의에 대한 구현 방법은 여러 가지(lock-based나 mvcc 등) 일 수 있다. 

---
# 각 격리 수준들(위키피디아 번역)
- DBMS의 네 가지 ACID 수준 중에서 Isolation 속성이 가장 자주 완화되는 속성이다. 
- 높은 수준의 격리성을 구현하기 위해 DBMS에서는 동시성에 손해를 보면서도 잠금을 사용하거나, MVCC를 사용한다. 
- 대부분의 DBMS들은 잠금 정도를 제어하는 다양한 트랜잭션 격리 수준들을 제공한다.
	- 많은 데이터베이스 애플리케이션의 경우, 가장 높은 격리 수준을 요구하지 않도록 구성하여 시스템에 대한 잠금 오버헤드를 줄일 수 있도록 한다.
- 각 격리 수준들은 아래 수준보다 강력하게, 계층형으로 구현되어 있다. 
	- 상위 격리 수준은 하위 격리 수준의 격리 수준을 포용한다.
- ANSI/ ISO SQL 표준에 따른 격리 수준은 아래와 같다. 

## Read uncommitted
- 가장 낮은 격리 단계로, 아직 커밋 되지 않은 변경들에 다른 트랜잭션들이 접근할 수 있다.
- Dirty reads 현상이 발생할 수 있다. 

## Read committed
- 커밋 된 변경 사항에 다른 트랜잭션들이 접근 가능하다. 
- 커밋 되지 않은 데이터에 대한 읽기, 즉 Dirty Reads를 방지한다.
- 그러나 트랜잭션이 읽기를 다시 실행하면 동일한 데이터를 찾을 것이라는 보장은 없고(Non-repeatable reads 발생), 범위 잠금도 존재하지 않는다(Phantom reads 발생).
- 잠금 기반 동시성 제어 DBMS 구현에서는 트랜잭션이 끝날 때 까지 쓰기 잠금을 유지하지만, 읽기 잠금은 SELECT 작업이 수행되는 즉시 해제된다. 

## Repeatable Reads
- 하나의 트랜잭션 속에서 동일 데이터에 대한 읽기를 다시 실행했을 때, 동일한 결과 값 리턴을 보장하는 격리 수준.
- 때문에 Non-Repeatable reads 이상 현상을 방지한다.
- 그러나 범위 잠금은 관리되지 않으므로 Phantom reads가 발생할 수 있다. 
- 잠금 기반 동시성 제어 DBMS 구현에서는 트랜잭션이 끝날 때 까지 읽기 및 쓰기 잠금을 유지한다. 
- 일부 시스템에서는 이 격리 수준에서 쓰기 왜곡(Write skew)가 발생할 수 있다. 
	- 쓰기 왜곡이란 한 테이블의 같은 칼럼에 대한 (이전에 해당 칼럼들을 업데이트 한 적 있는) 두 작성자들의 중복된 쓰기를 허용하는 현상으로, 두 트랜잭션 안에서 혼합된 데이터들이 존재하게 되는 이상 현상이다. 

## Serializable
- 가장 높은 격리 단계.
- 잠금 기반 동시성 제어 DBMS 구현에서는 Serializable 단계를 구현하기 위해, 트랜잭션이 끝날 때 읽기 및 쓰기 잠금 해제가 이루어진다. 
	- 또한 Phantom reads 현상을 방지하기 위해 SELECT 쿼리가 범위 지정된 WHERE 절을 사용할 때 범위 잠금을 획득해야 한다. 
- 비잠금 기반 동시성 제어를 사용하는 경우, 쓰기 충돌이 일어날 때 하나만 commit 할 수 있도록 한다. 
	- 보다 자세한 내용은 [[스냅샷 격리수준(Snapshot isolation)]] 참조. [출처](https://en.wikipedia.org/wiki/Isolation_(database_systems))
- Serializable 수준은 모든 트랜잭션이 순서대로 하나씩 실행되는 상태(직렬 스케줄, Serial Schedule)을 의미하지 않는다. 
	- 그 보다 직렬화 스케줄(Serializable Schedule) 상태로, 트랜잭션이 동시에 자료 접근 연산들을 교차하며 실행시켜도 켤과가 직렬 스케줄과 동일한 스케줄을 가지는(즉, 직렬성을 가지는) 격리 수준이라고 할 수 있다. [[트랜잭션 스케줄]] 참조.

---
> [!note] [[스냅샷 격리수준(Snapshot isolation)]]
> 트랜잭션에서 수행된 모든 읽기가 데이터베이스의 일관된 스냅샷을 볼 수 있도록 보장하는 격리 수준. 트랜잭션 자체는 해당 스냅샷 이후 발생한 동시 업데이트와 충돌하는 업데이트가 없는 경우에만 성공적으로 커밋된다.
> Serializability 이론과 그에 기반한 ANSI SQL-92 표준의 격리 수준 규약 대한 비판으로 등장하였고, 여러 DB에서 사용되고 있다고 함. 





---
# 참고문헌
[MySQL 공식 문서 - isolation lever](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)
[왜 트랜잭션 격리 수준을 나눴을까?](https://namuhuchutong.tistory.com/3)
[트랜잭션 격리 수준 - 세영씨 블로그](https://s-y-130.tistory.com/233#%EA%B-%BD%EC%-F%--%EC%--%--%ED%--%-C%---Race%--Condition-)
[영문 위키피디아](https://en.wikipedia.org/wiki/Isolation_(database_systems))
[영문 위키 번역 정리](https://seunghyunson.tistory.com/12)
[트랜잭션 격리성 수준](https://shuu.tistory.com/76)
[트랜잭션 스케줄](https://velog.io/@mrcocoball/220413%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98Transaction-%EC%8A%A4%EC%BC%80%EC%A4%84-%EB%A1%9D%ED%82%B9)