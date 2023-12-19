---
aliases:
  - Heap Area
  - 힙
  - 힙 영역
tags:
  - java
  - memory
  - heap
---

# Heap Area란?
- 프로그래밍 영역에서 [[Heap(Memory)|Heap Area 혹은 Heap]]는 동적으로 할당하는 메모리 공간을 지칭한다. 
- 이 문서는 메모리 영역의 일반적 힙 정의가 아닌, Java에서 Heap 공간이 어떻게 구현 되었는지를 설명한다. 

# 자바에서 Heap Area란?
- 자바에서 Heap Area, 힙 영역은 두 가지 관점으로 묘사한다.
	- [[JVM]]의 메모리 영역인 [[Runtime Data Area(Java)|Runtime Data Area]]의 구성 요소로서 Heap 영역
	- [[Java Memory 구조]]에서 Thread간 공유 영역으로서 Heap 영역 
		- 상기의 두 관점은 같은 대상에 대해서 다른 관점을 강조하고자 한 것이며, 두 관점에서 말하는 대상, 즉 Heap 영역 자체는 동일하다.

# Runtime Data Areas의 Heap area
- 힙 영역은 프로그램 상에서 데이터를 저장하기 위해 런타임 시 동적으로 할당하여 사용하는 메모리 공간으로 [[new 연산자]]를 통해 생성한 객체, 또는 인스턴스와 배열을 저장하는 공간이다. 
	- 다시 말해, [[new 연산자]]로 생성된 객체와 배열이 생성되는 영역이다. 
- [[참조 타입]]의 데이터들이 저장되는 영역이기도 하다. 
![[자바의 자료형과 힙 영역.png]]
- 자바에서 힙 영역의 [[메모리 관리]]는 [[Garbage Collection(java)|GC]]가 담당한다.

## Heap Area의 상세 구조
- Heap Area는 효율적인 [[Garbage Collection(java)|GC]]를 위해 2가지 영역으로 나누어 관리한다.
	- [[Young Generation]]
	- [[Ternured Generation]]

### Heap Area의 설계 원칙
- Heap영역은 효율적인 메모리 관리를 위해, [[Week Generational Hypothesis|약한 세대 가설]]에 기반해서 설계되었다.
![[Java 8 이후 Heap 구조.png]]

### Young Generation
![[Young Generation]]


### Ternured Generation
![[Ternured Generation]]


## Java 8 이전의 Heap 구조
- Java 7 버전 까지는 Heap에 (JVM Heap과 구분되는) [[Permanent Generation(Java)|PermGen]] 영역이 존재하였다.
- ![[힙 영역의 상세 구성(레거시).png]]
- 이 PermGen 영역은 후술할 단점들 때문에 [[Java 8|java 8]]부터 [[Deprecated]]되고, [[Metaspace]]로 교체되었다.
	- [[Metaspace]]는 [[Native Memory(Java)]]에 존재하는 영역이다.
- 다만 PermGen에서 담당하던 것들 중 [[String Pool]]은 Heap으로 넘어가게 되었다. 

### [[Permanent Generation(Java)|PermGen]] 영역
![[Permanent Generation(Java)|PermGen]]



# Heap Area의 사용 기간
[[일단 생략]]


## 레퍼런스
[GC- inpa blog](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)