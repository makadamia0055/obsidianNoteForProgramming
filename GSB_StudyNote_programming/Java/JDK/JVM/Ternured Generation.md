---
tags:
  - heap
  - Minor_GC
---
# Ternured Generation
- 힙 영역 내에 오래된 객체들이 저장되는 메모리 공간
- ![[Java 8 이후 Heap 구조.png]]
- Old 영역에 할당된 메모리가 허용치를 넘게 되면, Old 여역에 있는 모든 객체들을 검사하여 참조되지 않는 객체들을 한꺼번에 삭제하는 [[Garbage Collection(java)|GC]]가 일어난다. 
	- 이는 시간이 오래 걸리는 작업으로, 이 때 [[Garbage Collection(java)|GC]]를 실행하는 스레드를 제외한 모든 스레드는 작업을 멈추게 된다.
		- 이러한 현상을 [[Stop-the-world]]라고 한다.
	- 이렇게 [[Stop-the-world]]가 발생하고 Old 영역의 메모리를 회수하는 GC를 Major GC라고 한다. 

여기까지 정리하고 [[일단 생략]] 나중에 추가 정리하기