---
tags:
  - GC
aliases:
  - Minor GC
  - 마이너 GC
---

# Java 에서 Minor GC란?
![[Minor GC 과정 예시.png]]
- Minor GC는 [[Garbage Collection(java)|GC]] 기법 중 하나로, [[Heap Area(Java)|Heap Area]]의 [[Young Generation]]에서 일어나는 [[Garbage Collector|GC]]이다.
	- [[Java]]에서 새로 생성된 객체는 [[Young Generation]]의 [[Eden Space(java)|Eden]]에 생성되는데, 객체가 계속 생성되어 [[Eden Space(java)|Eden]] 영역이 꽉 차게 되었을 때 Minor GC가 일어난다.

## Minor GC의 과정
- [[Eden Space(java)|Eden]]이 꽉 차게 되면, [[Mark And Sweep]]에 따라 Minor GC가 일어난다.
	- Mark : [[Eden Space(java)|Eden]] 영역에서 [[Reachability|Reachable]]한 객체를 탐색한다.
		- [[Survivor Spaces(java)|from space]]에 객체가 존재한다면, 해당 객체들의 [[Reachability]]도 점검한다.
	- Mark 과정으로 [[Reachability|Reachable]]하다고 판정된 eden과 from space의 객체들을 비어있는 [[Survivor Spaces(java)|Survivor Space]]인 [[Survivor Spaces(java)|to space]]로 이동시킨다. 
	- Sweep : [[Reachability|Unreachable]]하다고 판정된 [[Object(java)|객체]]들의 메모리를 해제한다.
	- 이 과정을 통해 [[Survivor Spaces(java)|to space]]로 이동해, 살아남은 [[Object(java)|객체]]들의 [[Survivor Spaces(java)#Survivor Space의 주요 규칙|age]]를 1 씩 증가 시킨다.
		- age 값이 임계 값을 넘은 객체들(충분히 살아남은 객체들)은 Promotion되어 [[Ternured Generation]]으로 이동한다.
			- 만약 [[Survivor Spaces(java)|Survivor Space]]의 공간이 가득 찬 경우, 조기 Promotion이 일어난다.
				- 다만 이는 Minor GC라고 보기는 애매하고, Minor GC의 결과로 일어나는 현상

### Minor GC의 특성
- Minor GC는 일반적으로 관리 영역이 작기 때문에, 보통 0.5초~1초 사이에 끝난다.
	- 하지만 [[Major GC(java)|Major GC]]는 10 배 이상의 시간이 걸린다.