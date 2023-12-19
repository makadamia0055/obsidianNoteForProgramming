---
tags:
  - GC
aliases:
  - STW
  - Stop-The-World
---
- [[Garbage Collector|GC]]를 수행하기 위해 JVM이 프로그램 실행을 멈추는 현상을 의미
	- GC가 작동하는 동안 GC관련 [[Thread]]를 제외한 모든 Thread는 멈추게 되어 서비스 이용에 차질이 생길 수 있다.
	- 따라서 Stop-the-world 을 최대한 줄이는 것이 쟁점이다.
![[stop the world 예시.png]]

## 레퍼런스
- [GC- inpa blog](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)