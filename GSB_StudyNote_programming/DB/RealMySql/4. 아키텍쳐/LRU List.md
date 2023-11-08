![[버퍼 풀 관리를 위한 LRU 알고리즘 예시.png]]

 ## 버퍼 풀 LRU 리스트의 구성
 - LRU 리스트는 버퍼 풀에 접근이 잘 일어나는 데이터 페이지를 남기고, 잘 접근 되지 않는 페이지를 제거하기 위해 least recently used(LRU) 알고리즘을 사용하는 자료구조이다.
 - LRU 리스트는 상기의 그림처럼, Old Sublist와 New Sublist로 구성된다.
	 - Old Sublist
		 - LRU 알고리즘으로 구성
		 - 추방 대상이 선정되는 공간. 
		 - Old Sublist의 데이터를 읽으면, New Sublist의 꼬리로 옮겨간다.
		 - 기본적으로 버퍼 풀의 3/8을 차지함. 
	 - New Sublist
		 - ~~MRU(Most Recently Used) 리스트~~
		 - 쿼리에 의해 많이 사용되는 블록을 포함함.
		 - BufferPool에 존재하지 않는 데이터를 읽었을 경우 갱신되는 것으로, 자주 조회가 발생하는 Old Sublist를 유지하여 속도를 개선하는 것. 
	- ~~LRU 리스트는 엄밀하게 보면 LRU 서브리스트와 MRU 서브리스트로 구성되어 있다고 할 수 있다. ~~
		- LRU (Least Recently Used) 
			- 가장 오랫동안 사용되지 않은 항목을 제거.
			- 데이터가 재사용성이 높은 경우 유용
		- ~~MRU(Most Recently Used)~~
			- 가장 최근에 사용된 항목을 제거
			- 데이터 스트리밍과 같이 한 번 사용된 데이터가 곧바로 다시 사용되지 않을 가능성이 높은 경우 유용.
			- 즉, new sublist에서 MRU 알고리즘이 사용되는 이유는, 최근에 읽은 데이터가 곧바로 다시 사용되지 않을 가능성이 높기 때문.
		- 이렇게 함으로써, 자주 조회되는 데이터를 old sublist에 유지하고, 버퍼 풀의 전체 성능을 향상 시킬 수 있음.

> [!question] New Sublist != MRU list??
> LRU 리스트의 new sublist가 MRU(Most Recently Used)로 구성되어 있다는 설명은 Real MySQL 1권 109p 에서 나온 정보인데, 그 이외에 다른 레퍼런스에는 MRU라는 말이 나와있지 않다. 그리고 버퍼 전략에서 MRU 알고리즘은 최근에 엑세스된 데이터를 제거한다는 것인데, 이것이 해당 부분의 구성과 맞아 떨어지지 않는 듯 하다. 
> 때문에 단지 최근에 사용된 것 중심으로 기록한다는 의미로 mru라는 용어를 사용한 것이 아닌가 싶다.  

>[!note] 일단은 LRU 리스트는 LRU로 이해하자

---
 ### LRU 리스트의 기능과 작동 시나리오
 - LRU 리스트를 관리하는 목적은 디스크로부터 읽어온 페이지를 최대한 오랫동안 InnoDB 버퍼 풀의 메모리에 유지해서 디스크 읽기를 최소화 하는 것. 
 - InnoDB 스토리지 엔진에서 데이터를 찾는 과정은 다음과 같다. 
 - Real MySql의 설명-
	 1. 필요한 레코드가 저장된 데이터 페이지가 버퍼 풀에 있는지 검사
		 1. InnoDB 어댑티브 해시 인덱스를 이용해 페이지 검사
		 2. 해당 테이블의 인덱스(B-Tree)를 이용해 페이지를 검색
		 3. 버퍼 풀에 이미 데이터 페이지가 있었다면 해당 페이지 포인터를 New 방향으로 승급
	 2. 디스크에서 필요한 데이터 페이지를 버퍼 풀에 적재하고, 적재된 페이지에 대한 포인터를 Old sublist 헤더 부분에 추가(그림 상 Mid Point를 말하는 듯)
	 3. 버퍼 풀에 Old sublist 헤더 부분에 적재된 데이터 페이지가 실제로 읽히면 New sublist 방향으로 이동(Read Ahead와 같이 대량 읽기의 경우 디스크 데이터 페이지가 버퍼 풀로 적재는 되지만, 실제 쿼리에서 사용되지 않을 수 있으며, 이런 경우 New 방향으로 이동하지는 않음)
	 4. 버퍼 풀에 상주하는 데이터 페이지는 사용자 쿼리의 접근(Access)에 따라 Age가 부여되며, Aging에 따라 오래 접근 되지 않은 페이지는 제거된다. 새롭게 Access가 되면 Age는 초기화되며 new 방향으로 옮겨진다.
	 5. 필요한 데이터가 자주 접근 됐다면 해당 페이지의 인덱스 키를 어댑티브 해시 인덱스에 추가 
 - 다른 설명(더 나음)
	 - InnoDB_가 새로운 데이터 페이지를 버퍼 풀로 읽어오는 경우에는, midpoint (old sublist의 head)에 삽입한다. 
		 - 여기서 읽어오는 데이터에는 실제 사용자 쿼리에 의해 요청된 데이터도 있을 수 있지만, [read-ahead](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_read_ahead)에 의해 자동적으로 읽어오는 데이터도 포함된다.
	- old sublist_에 존재하는 페이지를 접근할 경우 해당 페이지는 “young”이라고 판단 되며, 버퍼풀 전체의 head (new sublist의 head)로 이동된다. 
		- 사용자 쿼리나 중간 작업에 의해 실제로 필요해서 접근된 페이지의 경우 곧바로 young으로 변경되지만, read-ahead 작업에 의해 접근된 페이지의 경우에는 young으로 취급하지 않는다.
	- 데이터베이스의 작업이 계속 진행됨에 따라서, 버퍼풀에 상주하는 데이터 페이지들 중 자주 접근 되지 않는 페이지는 자연스럽게 리스트의 tail로 이동하게 된다. 
		- 이는 직접적으로 데이터를 tail로 옮기는 것이 아닌, young 이 되는 페이지가 앞으로 이동하면서 발생한다. 
		- 추가로 old sublist의 페이지들은 새로운 페이지가 midpoint에 삽입 되면서 tail에 가까워지게 된다. 페이지 eviction시점까지 접근되지 않고 tail에 남아있는 페이지는 결국 버퍼 풀에서 삭제되게 된다.

- 왜 LRU list를 two sub-list로 나눠서 관리할까?
	- 자주 접근되는 hot page가 buffer pool에 계속 남겨지는걸 보장하기 위해서
	- never accessed again의 특성을 가지는 read-ahead 또는 scan같은 버퍼 풀 스캔이 buffer pool에 저장되는 양을 최소화하기 위해서
	- **read-ahead** 또는 full table index **scans**에 의해서 buffer pool이 변동되지 않도록 하기위해서

- 왜 midpoint에 insertion하는걸까?
	- Read-ahead 또는 large scans는 스캔동안 한 번만 스캔하고 다시 access하지 않는다는 특성을 가지고있기 때문에 midpoint에 insertion시켜 곧 쫓겨나게 하는것이다. midpoint insertion을 하지 않으면 head에 들어올것이고 나가는데 시간이 오래걸리기 때문이다.
	- [출처](https://velog.io/@rhtaegus17/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-MySQL-TPC-C2)


[공식문서](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html)

---