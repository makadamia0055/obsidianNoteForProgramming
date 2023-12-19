---
tags:
  - GC
  - java
aliases:
  - Major GC
---
# Java에서 Major GC란?
- Major GC란 [[JVM]] [[Heap Area(Java)|힙]] 영역의 [[Ternured Generation|Old Generation]]에서 일어나는 [[Garbage Collection(java)|GC]]이다.
- [[Ternured Generation|Old Generation]] 영역은 [[Young Generation]]에서 길게 살아남아 [[Promotion(GC)]]된 [[Object(java)|객체]]들이 존재하는 공간이다.
	- [[Survivor Spaces(java)|Survivor Space]]에서 [[Aging]]되어 설정된 age 값이 임계치를 넘은 객체들이나, [[Survivor Spaces(java)|Survivor Space]]공간이 가득 차 [[Promotion(GC)|조기 프로모션]]이 일어나는 경우에 [[Young Generation]]에서 해당 객체들이 넘어오게 된다.
- Major GC는 이렇게 [[Object(java)|객체]]들이 [[Promotion(GC)|Promotion]]되어 [[Ternured Generation|Old Generation]] 영역의 메모리가 부족하게 되면 발생하게 된다.
![[Major GC 예시.gif]]

### Major GC의 특징
- Major GC는 Old 영역의 데이터가 가득 차면 GC를 실행하는 단순한 방식이다. 
	- 다만 [[Ternured Generation|Old Generation]]의 크기가 크기 때문에, 메모리 상 객체 검증 및 제거에 많은 시간이 걸리게 된다.
		- [[Minor GC(java)|Minor GC]]보다 10배 이상의 시간이 걸린다.
	- 특히 이 시간 동안 GC 스레드 이외에 다른 스레드가 멈추는 [[Stop-the-world]] 현상이 일어난다.
		- [[Stop-the-world]] 자체는 [[Minor GC(java)|Minor GC]]에서도 일어나지만([출처](https://dzone.com/articles/minor-gc-vs-major-gc-vs-full)), [[Minor GC(java)|Minor GC]]에서는 무시할 수 있는 정도의 시간이 걸린다.
- Major GC를 [[Full GC(java)|Full GC]]와 동의어로 분류하는 곳도 있으나, 별도의 GC로 분류하는 곳도 있어 문서를 분리하였다.

## 레퍼런스
- 인파 블로그
- [마이너gc vs 메이저 gc vs full gc](https://dzone.com/articles/minor-gc-vs-major-gc-vs-full)