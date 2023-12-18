---
aliases:
  - Runtime Data Area
  - 런타임 데이터 영역
---
---
# Runtime Data Area란?
- 런타임 데이터 영역은 JVM의 메모리 영역으로, 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 메모리 영역.

## Runtime Data Area의 구조
- 런타임 데이터 영역은 아래와 같은 요소로 구성되어 있다. 
![[Runtime Data Areas의 구조.png]]
- 상기 구성 요소들은 스레드 간 공유 가능한 공통 영역과 스레드 마다 할당 되는 스레드 영역으로 구분 할 수 있다. 
	- 공통 영역(=모든 스레드 간 공유 가능한 영역, [[Garbage Collection(java)|GC]]의 대상)
		- [[Method Area]]
			- 메소드 영역 내의 [[Runtime Constant Pool]]
		- [[Heap Area(Java)|Heap Area]]
	- 스레드 마다 하나 씩 생성되는 영역
		- [[Stack Area(Java)]]
		- [[PC Register(memory area)]]
		- [[Native Method Stack]]
![[Runtime Data Area의 스레드 별 메모리 할당.png]]

![[Runtime Data Areas의 구조2.png]]
### [[Method Area]]
![[Method Area]]


### [[Runtime Constant Pool]]
![[Runtime Constant Pool]]


### [[Heap Area(Java)|Heap Area]]
![[Heap Area(Java)]]


---
### [[Stack Area(Java)]]
![[Stack Area(Java)]]


### [[PC Register(memory area)|PC Register]]
![[PC Register(memory area)]]


### [[Native Method Stack]]
![[Native Method Stack]]