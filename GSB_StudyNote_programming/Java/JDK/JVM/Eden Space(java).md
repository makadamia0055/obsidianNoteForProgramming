---
tags:
  - heap
  - memory
aliases:
  - Eden
  - 에덴
---
# Eden Space란?
![[Java 8 이후 Heap 구조.png]]
- Java에서 [[Heap Area(Java)|Heap Area]]의 [[Young Generation]] 아래 존재하는 메모리 영역
- 새로 생성된 [[Object(java)|객체]]가 위치하는 공간.
	- 즉, [[new 연산자]]를 통해 새로 생성된 객체가 위치하는 곳이다.
- Eden 영역이 가득 차면 [[Minor GC(java)]]가 발생하며, 이 과정에서 [[Mark And Sweep]]이 일어난다.
	- [[Mark And Sweep]] 과정이 일어난 후 아직 사용중인 객체는 [[Survivor Spaces(java)|Survivor Space]]로 이동하며, Eden 영역은 비워진다.

## 레퍼런스
- GPT(edge)