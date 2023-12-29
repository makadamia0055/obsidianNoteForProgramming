---
tags:
  - heap
  - Minor_GC
aliases:
  - Ternured Generation
  - Old Generation
---
# Ternured Generation
- 힙 영역 내에 오래된 객체들이 저장되는 메모리 공간
	- 즉, [[Young Generation]]에서 [[Reachability|Reachable]]한 상태를 유지하여 살아남은 객체가 복사되는 영역이다.
- [[Young Generation]]보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다.
- Ternured Generation 에서는 [[Major GC(java)|Major GC]], [[Full GC(java)]]가 일어난다.
- ![[Java 8 이후 Heap 구조.png]]
- Old 영역에 할당된 메모리가 허용치를 넘게 되면, Old 영역에 있는 모든 객체들을 검사하여 참조되지 않는 객체들을 한꺼번에 삭제하는 [[Garbage Collection(java)|GC]]가 일어난다. 
	- 이는 시간이 오래 걸리는 작업으로, 이 때 [[Garbage Collection(java)|GC]]를 실행하는 스레드를 제외한 모든 스레드는 작업을 멈추게 된다.
		- 이러한 현상을 [[Stop-the-world]]라고 한다.
	- 이렇게 [[Stop-the-world]]가 발생하고 Old 영역의 메모리를 회수하는 GC를 [[Major GC(java)|Major GC ]]라고 한다. 

- Old 영역에서 Young 영역으로 참조가 일어나는 경우, 해당 참조를 기록하는 카드 테이블이 있다고 한다.([[일단 생략]])