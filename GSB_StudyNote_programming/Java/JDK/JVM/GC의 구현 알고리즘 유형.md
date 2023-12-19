---
tags:
  - GC
  - algorithm
aliases:
  - GC의 알고리즘 유형
---
# GC의 구현 알고리즘 유형 정리
- [[Garbage Collection(java)|GC]]의 알고리즘 유형은 대표적으로 다음과 같이 정리할 수 있다.
	- [[Serial GC]]
		- 단일 [[Thread|스레드]] 환경에 맞게 설계되었으며, [[Garbage Collector|GC]]를 수행할 때 하나의 스레드만 사용.
		- 따라서 이 GC 구현은 실행중인 모든 어플리케이션 스레드를 [[Stop-the-world|일시 중지]]함
	- [[Parallel GC]]
	- [[Parallel Old GC]]
	- [[CMS GC]]
	- [[G1 GC]]
	- [[Shenandoah GC]]
	- [[ZGC]]

[[일단 생략]]
쓰다가 좀 피곤하기도 하고, 나중에 다시 정리하던지 아님 안하던지.



## 레퍼런스
- [인파 블로그 GC](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)