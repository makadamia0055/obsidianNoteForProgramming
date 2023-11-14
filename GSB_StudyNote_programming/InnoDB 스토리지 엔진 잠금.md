---
tags:
---
---
- InnoDB 스토리지 엔진은 MySQL에서 제공하는 잠금과는 별개로 스토리지 엔진 내부에서 **레코드 기반 잠금 방식**을 탑재하고 있다.
	- 레코드 기반의 잠금 방식 때문에 뛰어난 동시성 처리를 제공할 수 있다.
	- 하지만 이원화된 잠금 처리 탓에 InnoDB 스토리지 엔진에서 사용되는 잠금에 대한 정보는 MySQL 명령을 이용해 접근하기가 상당히 까다롭다. 
		- 이전 버전의 MySQL 서버에서는 InnoDB의 잠금 정보 진단 도구로 
			- lock_monitor 생성
				- innodb_lock_monitor라는 이름의 테이블을 생성해 InnoDB잠금 정보를 덤프하는 방법
			- `SHOW ENFINE INNODB STATUS` 명령
				- 거의 어셈플리 코드 같아서 이해 어려움.
		- 최신 버전에서는 InnoDB의 트랜잭션과 잠금, 그리고 잠금 대기 중인 트랜잭션의 목록을 조회할 수 있는 방법이 도입되었다.
			- MySQL 서버의 `information_schema` 데이터베이스에 존재하는 `INNODB_TRX`, `INNODB_LOCKS`, `INNODB_LOCK_WAITS` 테이블을 조인해서 조회하면 현재 잠금을 대기하는 트랜잭션과, 잠금을 보유하는 트랜잭션의 조회 가능.
			- 또 장시간 잠금을 가지고 있는 클라이언트를 찾아서 종료시킬 수도 있음.
			- `Performance Schema`를 이용해 InnoDB 스토리지 엔진의 내부 잠금([[세마포어]])에 대한 모니터링 방법도 추가됨.

# InnoDB 스토리지 엔진의 잠금


![[InnoDB 잠금의 종류.png]]