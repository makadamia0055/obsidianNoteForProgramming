---
tags:
  - heap
  - Minor_GC
aliases:
---

# Young Generation
- [[Heap Area(Java)|Heap Area]] 내에서 자바 객체가 생성되자마자 저장되는 공간
	- 생긴지 얼마 안되는 [[객체]]가 저장되는 공간.
	- Eden과 2개의 Survivor로 구성되어 있다. 
	- ![[Java 8 이후 Heap 구조.png]]
- Heap 영역에 객체가 생성되면 최초에는 Eden 영역에 할당된다.
	- 그리고 이 영역에 데이터가 어느 정도 쌓이게 되면 참조 정도에 따라 survivor의 빈 공간으로 이동되거나 회수된다.
		- 이 때, Young Generation과 [[Ternured Generation]]에서의 [[Garbage Collection(java)|GC]]를 Minor GC라고 한다. 