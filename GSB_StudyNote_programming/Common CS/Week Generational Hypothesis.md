---
tags:
  - heap
aliases:
  - Week Generational Hypothesis
  - 약한 세대 가설
---
# Week Generational Hypothesis란?
- Week Generational Hypothesis(약한 세대 가설)은 [[JVM]]의 [[Garbage Collection(java)|GC]] 설계의 기반이 되는 가설이다.
- 이 가설은 다음 두 가지를 전제로 한다.
	1. 대부분의 객체는 금방 접근 불가능한 상태([[Reachability|Unreachable]])가 된다.
	2. 오래된 객체에서 새로운 객체로의 [[Reference|참조]]는 아주 적게 존재한다.
		- 즉, 대부분의 객체는 일회성이며, 메모리에 오래 남아있는 경우는 드물다.
- 이 가설을 바탕으로, [[Garbage Collection(java)|GC]]는 모든 객체를 검사하여 메모리를 해제하지 않는다.
	- 대신, 접근 불가능한 상태가 된 객체들만을 대상으로 메모리를 회수한다.
- 이 가설은 실제로 여러 관찰을 통해 높은 경향성을 가지는 것으로 증명되었다.



## 레퍼런스 
- GPT(edge로 물어봄)