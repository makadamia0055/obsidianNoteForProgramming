---
tags:
  - GC
  - Full_GC
aliases:
  - Full GC
  - 풀 GC
---
# Full GC란?
- Full GC는 [[Young Generation]]과 [[Ternured Generation|Old Generation]] 두 영역 모두, 즉 전체 [[Heap Area(Java)|힙]] 영역에 대한 [[Garbage Collection(java)|GC]]를 말한다. 
	- Full GC는 종종 [[Ternured Generation|Old Generation]]에 대한 [[Garbage Collection(java)|GC]]인 [[Major GC(java)|Major GC]]와 같은 것으로 간주되곤 한다.
		- 그러나 [[Java]] 내부에서 별도의 루틴으로 간주된다는 글이 있어서, 별도의 문서로 분리했다.
		- 즉 Full GC = [[Minor GC(java)|Minor GC]] + [[Major GC(java)|Major GC]]


## 레퍼런스
- [마이너gc vs 메이저 gc vs full gc](https://dzone.com/articles/minor-gc-vs-major-gc-vs-full)