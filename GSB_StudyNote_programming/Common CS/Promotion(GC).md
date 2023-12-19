---
tags:
  - GC
  - promotion
aliases:
  - Promotion
  - 프로모션
  - 조기 프로모션
  - 조기 Promotion
---
# GC에서 Promotion이란?
- GC에서 Promotion이란([[Java]] 기준으로 설명), [[Heap Area(Java)|힙]] 메모리의 [[Young Generation]]에서 [[Ternured Generation|Old Generation]]으로 객체가 이동하는 현상을 Promotion이라고 한다.
	- [[Young Generation]]의 [[Survivor Spaces(java)|Survivor Space]]에 존재하는 객체들은 살아남을 때마다 age 값을 부여 받게 된다.([[Aging]])
		- 거듭된 [[Minor GC(java)|Minor GC]]에서 살아남아 설정된 임계 치 값을 넘은 [[Object(java)|객체]]는 Promotion, 즉 승급 되어 [[Young Generation]]에서 [[Ternured Generation|Old Generation]]으로 이동하게 된다. 
![[Promotion(GC)의 예시.png]]
### Promotion의 age값(=임계 치)
- [[JVM]] 중 가장 일반적인 [[HotSpot JVM]]의 경우, age 임계 값이 31로 설정되어 있다.
	- (6bit라서)

### 조기 프로모션
- 간혹 Eden이 가득 차서 옮겨야 하는데, Survivor 영역도 가득 차면, age 값이 임계 값 미만인 객체들이라고 할 지라도, [[Ternured Generation]]으로 [[Promotion(GC)|조기 Promotion]]이 일어난다.