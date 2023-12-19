---
tags:
  - heap
  - java
  - GC
aliases:
  - Survivor Space
  - 서바이버
  - survivor 0
  - survivor 1
  - from space
  - to space
---
# Survivor Spaces란?
![[Java 8 이후 Heap 구조.png]]
- Java에서 [[Heap Area(Java)|Heap Area]]의 [[Young Generation]] 아래 존재하는 Survivor Space
	- 보통 Survivor Space는 2개가 존재한다.
	- 이 각각의 Survivor Space를 "Survivor 0", "Survivor 1"로 호칭한다.
		- 혹은 [[Minor GC(java)|Minor GC]]시의 역할에 따라 From Space, To Space라고 하기도 한다.
	- 보통 2개라는 말은 [[JVM]]의 구현이나 [[GC 알고리즘]] 설정에 따라, 그 수나 구조가 다를 수 있다는 것을 의미한다.
- [[Young Generation]]에는 Survivor space 이외에도 [[Eden Space(java)]]가 존재한다.
	- [[Eden Space(java)|Eden]]에서 [[Minor GC(java)|Minor GC]]를 거친 [[Object(java)|객체]]가 Survivor 영역으로 넘어온다.
		- 즉, Survivor 영역에 존재하는 객체들은 최소한 1번의 [[Garbage Collection(java)|GC]]를 거쳤다는 것이다.

## Survivor Space의 주요 규칙
- Survivor 0 과 Survivor 1 둘 중 하나의 영역은 무조건 비어있어야 한다.
	- 즉 하나의 Survivor 영역은 무조건 비어있어야 한다.
- 이는 [[Garbage Collection(java)|GC]] 알고리즘에서 [[Object(java)|객체]]의 생명주기를 효율적으로 관리하기 위해서다.
	- [[Eden Space(java)|Eden]]에서 [[Minor GC(java)|Minor GC]]가 발생하면, 그 중 살아남은 객체가 하나의 Survivor 영역으로 이동하고, 또 그Survivor 영역이 가득 차면, 그 중에 살아남은 객체를 다른 비어있는 Survivor 영역으로 이동시키고, 원래의 Survivor 영역은 비우게 된다. 
		- 이러한 객체 간의 영역 이동을 [[객체 이동 기법]]이라고 한다.
		- 이 과정에서 Survivor Space에서 살아남은 객체들에게 age 값을 부여하여 [[Aging]]을 한다.
			- age 값이 일정치 이상 된 [[Object(java)|객체]]들은 [[Ternured Generation|Old Generation]]로 이동시킨다.
				- 이러한 이동을 [[Promotion(GC)|Promotion]]이라고 부른다.
			- 간혹 Eden이 가득 차서 옮겨야 하는데, Survivor 영역도 가득 차면, age 값이 임계 값 미만인 객체들이라고 할 지라도, [[Ternured Generation]]으로 [[Promotion(GC)|조기 Promotion]]이 일어난다.


## 레퍼런스
- [linuxism :: java - JVM 메모리 및 옵션 (ip.or.kr)](https://linuxism.ustd.ip.or.kr/291)
- [GC - inpa blog](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
- [Java Garbage Collection (naver.com)](https://d2.naver.com/helloworld/1329)
- [GC stop the world 를 알아보자! (velog.io)](https://velog.io/@limsubin/GC-stop-the-world-%EB%9E%80)
- [마이너 GC vs 메이저 GC vs 풀 GC - DZone](https://dzone.com/articles/minor-gc-vs-major-gc-vs-full)