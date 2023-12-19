---
tags:
  - legacy
  - java
  - Metaspace
  - heap
aliases:
  - PermGen
---
# Permanent Generation이란?
- [[Java 8|java 8]] 이전 버전까지 존재하던 힙 메모리 영역([[Heap Area(Java)|Heap Area]]과는 별도로 분류됨)으로, 로드 된 클래스 메타데이터를 추적하여, 모든 정적 컨텐츠를 저장하던 공간.
	- [[Java 8|java 8]]에서 [[Deprecated]]되어 [[Metaspace]] 메모리 영역으로 대체 됨. 
	- 줄여서 PermGen이라고 부름.

## PermGen 의 구성
- Permanent Generation은 주 메모리 [[Heap Area(Java)|Heap Area]]과 분리된 특수 힙 공간이었다.
- JVM은 PermGen에 모든 정적 컨텐츠를 저장했다.
	- 모든 [[static method]], [[Primitive Type(java)]] 변수, 정적 개체에 대한 참조가 포함된다.
	- 또한 바이트 코드, 이름 및 JIT 정보에 대한 데이터가 포함되어 있었다.
	- [[String Pool]] 또한 Java 7까지는 PermGen에 속해 있었다. 

## PermGen의 단점
- PermGen 영역은 메모리 크기가 제한되어 있었다.
	- PermGen의 메모리 크기는 설정한 값에 따라 동적 확장이 변경 가능했으나, 결국 JVM에 의해 크기가 강제 된다.[출처](https://johngrib.github.io/wiki/java8-why-permgen-removed/)
		- -XX:PermSize=[size] 로 PermGen의 초기(최소) 크기를 설정 가능
		- -XX:MaxPermSize=[size]로 최대 크기 설정 가능.
			- JDK 8 버전 이상에서는 상기의 튜닝 플래그를 사용하면 경고 표시가 뜬다.
	- PermGen의 기본 크기는 JVM이 설치된 시스템에 따라
			- 32bit에서는 64MB
			- 64bit에서는 84MB
- 메모리 크기가 제한되어 있기 때문에 PermGen은 [[OutOfMemory]] 에러 생성을 일으켰다.
	- [[Class Loader]]가 제대로 [[Garbage Collection(java)|GC]] 되지 않아 [[Memory Leak]]가 발생했다.
- 이러한 단점들로 결국 [[Java 8|java 8]]부터 [[Metaspace]]로 대체된다.
	- 다만, 기존 Perm 영역에 존재하던 Static Object는 Heap 영역으로 옮겨져서 GC의 대상이 최대한 될 수 있도록 하였다.


## 레퍼런스 
- [JDK 8에서 Perm 영역은 왜 삭제됐을까 - 기계인간 John Grib](https://johngrib.github.io/wiki/java8-why-permgen-removed/)
- [Java의 Permgen 대 Metaspace | (주)바엘둥 (baeldung.com)](https://www.baeldung.com/java-permgen-metaspace)