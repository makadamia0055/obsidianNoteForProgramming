- MySQL 엔진에서는 다음과 같은 잠금을 제공한다. 
	- **글로벌 락**
	- 테이블 데이터 동기화를 위한 **테이블 락**
	- 테이블의 구조를 잠그는 **메타데이터 락**
	- 사용자의 필요에 맞게 사용할 수 있는 **네임드 락**

---

# 글로벌 락(GLOBAL LOCK)
- MySQL에서 제공하는 잠금 가운데 가장 범위가 큰 잠금
- `FLUSH TABLES WITH READ LOCK` 명령으로 획득 가능하다. 
	- TABLES는 락을 거는 대상을 지칭하는 명령어.(이 명령어는 특정 테이블이 아니라 MySQL 모든 테이블을 지칭하므로 특정 테이블이 생략)
	- `UNLOCK TABLES;` 로 해제
	- READ LOCK라는 표현처럼 읽기 락(읽기 위한 락이므로 쓰기를 잠금), 즉 공유 락(Shared lock)이다.
- 영향을 미치는 범위 : MySQL 전체
	- 작업 대상 테이블이나 DB가 다르더라도 동일하게 영향을 미친다.
		- 여러 DB에 존재하는 MyISAM이나 MEMORY 테이블에 대해 mysqldump로 일관된 백업을 받아야 할 때는 글로벌 락을 사용한다. 
	- 일단 한 세션에서 글로벌 락을 획득하면, 다른 세션에서 SELECT를 제외한 대부분의 DDL이나 DML 쿼리는 글로벌 락이 해제될 때까지 대기 상태로 남는다.


> [!warning]
> 글로벌 락을 거는 FLUSH TABLES WITH READ LOCK 명령어는 실행과 동시에 MySQL 서버에 존재하는 모든 테이블을 닫고 잠금을 건다. 
> 읽기 잠금을 걸기 때문에, 선행하는 트랜잭션에서 쓰기 잠금을 건 경우, 해당 트랜잭션이 완료될 때 까지 기다려야 한다. 
> 또, FLUSH TABLES WITH READ LOCK 명령어는 읽기 잠금을 걸기 전에 먼저 테이블을 플러시 해야 하기 때문에, 테이블에 실행 중인 모든 종류의 쿼리가 완료돼야 한다. 
> 이렇게 글로벌 락은 MySQL 서비스의 모든 테이블에 큰 영향을 미치기 때문에 웹 서비스 용으로 사용되는 MySQL 서버에서는 가급적 사용하지 않는 것이 좋다. 
> mysqldump 같은 백업 프로그램은 내부적으로 백업을 위해 이 명령을 걸기도 한다. 

---

 ### 백업락
 - 기존 글로벌 락보다 가벼운 글로벌 락
	 - MySQL 8.0부터 기본 스토리지 엔진으로 채택된 InnoDB에서는 트랜잭션을 지원하기 때문에, 일관된 데이터 상태를 위해서 모든 데이터 변경 작업을 멈출 필요는 없다.
	 - 때문에 조금 더 가벼운 글로벌 락인 백업 락이 등장하게 되었다.
- 설정법
	 - `LOCK INSTANCE FOR BACKUP;`로 설정
	 - `UNLOCK INSTANCE;`로 해제
 - Xtrabackup이나 Enterprise Backup와 같은 백업 툴에서 사용. 
 - 백업 락의 기능
	 - 모든 세션에서 테이블의 스키마나 사용자의 인증 관련 정보를 변경할 수 없게 된다.
		 - 데이터 베이스 및 테이블 등 모든 객체 생성 및 변경, 삭제
		 - REPAIR TABLE과 OPTIMIZE TABLE 명령
		 - 사용자 관리 및 비밀번호 변경
	 - 하지만 백업 락은 일반적인 테이블의 데이터 변경은 허용된다.
 - 백업 락의 도입 이유(상세)
	 - 일반적인 MySQL 서버의 구성은 소스 서버(Source server)와 레플리카 서버(Replica server)로 구성되는데, 주로 백업은 레플리카 서버에서 실행된다. 
	 - [[일단 생략]] 레플리케이션 좀 더 본 뒤로 쓰기로 Real MySQL 1권 162p


---
# 테이블 락(Table Lock)
- 개별 테이블 단위로 설정되는 잠금
- 명시적 또는 묵시적으로 특정 테이블의 락을 획득할 수 있다.
	- 명시적으로는 `LOCK TABLES table_name [ READ | WRITE ]`로 특정 테이블의 락을 획득할 수 있다. 
		- 명시적으로 획득한 잠금에 대한 반환은 `UNLOCK TABLES` 명령어로 가능
	- 그러나 일반적인 상황에서 명시적으로 테이블을 잠그는 작업은,(글로벌 락과 마찬가지로) 온라인 작업에 상당한 영향을 미치기 때문에, 사용할 필요가 거의 없다. 
- 테이블 락은 MyISAM, MEMEORY테이블 뿐만 아니라 InnoDB를 사용하는 테이블도 동일하게 설정 가능.
- 묵시적 테이블 락은 MyISAM, MEMORY 테이블에 데이터를 변경하는 쿼리를 실행하면 발생한다. 
	- MySQL 서버가 데이터가 변경되는 테이블에 잠금을 설정하고 데이터 변경 후, 즉시 잠금을 해제하는 형태로 사용.
		- 즉, 묵시적인 테이블 락은 쿼리 실행되는 동안 자동으로 획득했다가, 쿼리 완료 후 자동 해체.
- 그러나 InnoDB 테이블이 경우, 스토리지 엔진 차원에서 레코드 기반의 잠금을 제공하기 때문에 단순 데이터 변경 쿼리로 인해 묵시적인 테이블 락이 설정되지는 않는다. 
	- 대부분의 데이터 변경 쿼리(DML)에서는 무시되고, **스키마를 변경하는 쿼리(DDL)경우**에만 영향을 미친다. 

---

# 네임드 락
- 유저 레벨 락으로도 불린다. 
- `GET LOCK() 함수`를 이용해 **임의의 문자열**에 대해 잠금을 설정할 수 있다. 
	- 즉, 데이터베이스 객체(테이블, 레코드, AUTO_INCREMENT 등)이 대상이 아니라, 사용자가 지정한 문자열(String)에 대한 잠금이다.
- 자주 사용되지는 않지만 여러 클라이언트 간의 상호 동기화 처리 등에서 활용된다. 
	- ex) 데이터베이스 서버 1개에 5대의 웹 서버가 접속해서 서비스 하는 상황에서, 5대의 웹서버가 어떤 정보를 동기화 해야 할 때.
	- 또는, 많은 레코드에 대해서 복잡한 요건으로 레코드를 변경하는 트랜잭션에 유용. 
		- 배치 프로그램처럼 한꺼번에 많은 레코드를 변경하는 쿼리는 데드락을 자주 유발한다.
			- 이럴 때, 각 프로그램의 실행 시간 분산, 프로그램 코드 수정 등의 번거럽고 불완전한 해결책 대신 네임드 락을 사용할 수 있다.
				- 동일 데이터를 변경하거나 참조하는 프로그램끼리 분류해서 네임드 락을 걸고 쿼리 실행.
- GET LOCK() 사용방법
```
--// "mylock"라는 문자열에 대한 잠금 획득
mysql> SELECT GET_LOCK('mylock', 2);
--// 2라는 인수는 이미 잠금을 사용 중일 때, 대기 시간을 의미(2초 이후 자동 잠금 해제)

--// "mylock"라는 문자열에 대한 잠금 설정 확인
mysql> SELECT IS_FREE_LOCK('mylock');

--// "mylock"이라는 문자열에 대해 획득했던 잠금을 반납(해제)
mysql> SELECT RELEASE_LOCK('mylock');

--// 3개 함수 모두 정상적으로 락을 획득하거나 해제한 경우 1 리턴, 아니면 NULL이나 0 반환.

```

- MySQL 8.0 버전부터는 네임드 락의 중첩 설정과 현재 세션에서 획득한 네임드락 일괄 해제 기능도 추가 되었다. 
```
--// 중첩 설정
mysql> SELECT GET_LOCK('mylock_1', 10);
mysql> SELECT GET_LOCK('mylock_2', 10);

--// 일괄 해제
mysql> SELECT RELEASE_ALL_LOCKS();
```

---

# 메타데이터 락
- 데이터 베이스 객체(테이블이나 뷰 등)의 **이름**이나 **구조**를 변경하는 경우에 획득하는 잠금. 
- 메타데이터 락은 명시적으로 획득하거나 해제할 수 없고, **자동으로(묵시적으로) 획득하는 잠금**이다. 
- 메타데이터 락 작동 예시들
	- 테이블 이름이나 구조 변경
		- RENAME TABLE name1 TO name2; 같이 테이블 이름을 변경하는 경우 자동 획득
			- RENAME TABLE 명령의 경우 원본 이름과 변경될 이름 두 개 모두 한꺼번에 잠금 설정
	- 실시간으로 테이블을 바꿔야 하는 요건
		- 하나의 쿼리에서 두 작업을 동시에 진행시켜 메타 데이터 락을 공유하여 사용.
		- 배치 프로그램에서 자주 발생

```
--// 배치 프로그램에서 별도의 임시 테이블(rank_new)에 서비스용 랭킹 데이터를 생성
--// 랭킹 배치가 완료되면 현재 서비스용 랭킹 테이블(rank)를 rank_backup로 백업하고
--// 새로 만들어진 랭킹 테이블(rank_new)을 서비스용으로 대체하고자 하는 경우
mysql> RENAME TABLE rank TO rank_backup , rank_new TO rank;
--// 이렇게 하나의 RENAME 명령문에 두 개의 RENAME 작업을 한꺼번에 실행하면, rank 테이블의 부재 없이 변경작업 가능.
--// 그런데 이걸 두 개로 나누어 작업하면, 잠깐의 순간이지만 rank 테이블이 부재하는 순간이 발생하여 "Table not found 'rank'"같은 상황 발생 가능
mysql> RENAME TABLE rank TO rank_backup;
mysql> RENAME TABLE rank_new TO rank;
```
- 메타데이터 잠금과 InnoDB 트랜잭션을 동시에 사용해야 하는 경우
[[일단 생략]]

---




 



---
### 참고 문헌
[RealMySQL 정리한 벨로그](https://velog.io/@fortice/MySQL-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%9E%A0%EA%B8%88Lock)
[MySQL에서 사용하는 Lock 이해 : 네이버 블로그 (naver.com)](https://m.blog.naver.com/hanajava/220732781633)
[MYSQL 락과 트랜잭션 (velog.io)](https://velog.io/@denhur62/MYSQL-%EB%9D%BD%EA%B3%BC-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98)
[트랜잭션 ACID 특성과 Isolation Level. 안녕하세요! BE 직방부동산팀에서 근무중인 Jeff 입니다. 이번… | by 김범준 | 직방 기술 블로그 | Medium](https://medium.com/zigbang/%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-acid-%ED%8A%B9%EC%84%B1%EA%B3%BC-isolation-level-2e918ac1b376)
