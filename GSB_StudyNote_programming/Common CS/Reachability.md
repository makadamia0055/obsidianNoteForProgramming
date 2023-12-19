---
tags:
  - Reference
aliases:
  - Reachable
  - Unreachable
---
# Reachability란?
- 객체가 도달성, 도달능력, 도달가능성이 있는지 여부를 따지는 성질로, 현재 특정 객체에게 도달할 수 있는가를 판별한다.
	- 여기서 도달 가능하다는 것은 객체에 대한 [[Reference]]가 있다는 것이다.
		- 이렇게 도달 가능한 상태를 Reachable, 도달 불가능한 상태를 Unreachable라고 한다.
- 객체에 대한 Reachability 여부는 [[Garbage Collector|GC]]에서 GC 대상을 가르는 중요한 요건이 된다.

## 연관 개념
- [[Garbage Collection(java)|GC]]
- [[Root Set(java)|Root Set]]