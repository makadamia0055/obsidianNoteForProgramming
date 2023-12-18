---
tags:
  - java
  - jvm
  - memory
aliases:
  - 가비지 컬렉터
  - 가비지 컬렉션
  - Garbage Collection
  - GC
---
# Java에서 GC란?
- Java는 대표적인 [[Managed language]]로, 프로그래밍 언어 자체가 메모리의 할당과 해제를 관리하는 언어이다. 
- Java의 Garbage Collection은 자바의 메모리 관리 방법 중 하나로, [[JVM]]의 [[Heap Area(Java)]]에서 동적으로 할당했던 메모리 영역 중 필요 없게 된 메모리 영역을 주기적으로 삭제하는 프로세스를 말한다. 
	- Garbage Collector은 이러한 프로세스를 담당하는 모듈을 칭한다. 

## GC의 단점
- 메모리가 언제 해제되는지 정확하게 알 수 없어 제어하기 힘들다.
- 가비지 컬렉션이 동작하는 중에는 [[Stop-the-world]]가 발생하여 다른 동작을 멈추기 때문에 오버헤드가 발생한다.
	- 이러한 단점들로 GC가 너무 자주 실행되면 소프트웨어 성능 하락의 문제가 되기도 한다.
	- 따라서 어플리케이션의 사용성을 유지하면서 효율적이게 GC를 실행하는 최적화 작업이 개발자의 숙제가 된다.
		- 이러한 최적화 작업을 GC 튜닝이라고 한다. 

# GC의 동작 과정

## GC의 대상이 되는 객체들
![[Unreachable한 객체와 GC.png]]

- [[Object(java)|객체]]들은 실질적으로 [[Heap Area(Java)|Heap]] 영역에서 생성되고 [[Method Area]]나 [[Stack Area(Java)|Stack Area]] 등에서 힙에 생성된 객체의 주소만 참조한다.
	- 이렇게 메모리에서 직접 접근할 수 있는 모든 객체들을 [[Root Set(java)|Root Set]]이라고 한다.
- 이렇게 생성된 Heap의 객체들은 메서드가 끝나는 등의 특정 이벤트들로 인해, 해당 객체의 메모리 주소를 가지고 있는 참조 변수가 삭제되면서 Unreachable하게 된다.
	- 이러한 Unreachable 한 객체들이 GC의 대상이 된다.

## [[Mark And Sweep]]
![[Mark And Sweep]]

## Heap에서의 GC 과정
### Heap의 구조

### Minor GC


### Major GC


# GC 알고리즘


[[일단 생략]]

## 레퍼런스
[GC 동작 원리와 알고리즘 총정리- inpa 블로그](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
[GC - coding Factory](https://coding-factory.tistory.com/829)
[OOP, JVM 면접준비](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
[Guide to Garbage Collector Roots | Baeldung](https://www.baeldung.com/java-gc-roots)
[Guide to System.gc() | Baeldung](https://www.baeldung.com/java-system-gc#garbage%20collection)