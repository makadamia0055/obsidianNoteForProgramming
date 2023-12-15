---
tags:
  - jvm
  - execution_engine
aliases:
  - 실행엔진
---
---
# Execution Engine란?
- 실행엔진은 JVM의 구성요소 중 하나로, JVM 내에 배치된 [[.class(java)|.class 파일]](바이트코드)를 실제로 실행시키는 역할을 하는 런타임 모듈이다.
	- 자바의 바이트코드는 [[Class Loader|클래스로더]]에 의해 JVM 내의 [[Runtime Data Area(Java)|Runtime Data Area]]의 [[Method Area]]에 적재 되는데, 실행 엔진은 해당 위치의 바이트 코드를 실행하는 역할을 한다.
	- 실행 엔진은 바이트코드를 명령어 단위로 읽어서 실행한다.
	
## Execution Engine의 구조 
- 실행 엔진은 [[인터프리터]]와 [[JIT 컴파일러]] 두 가지 방식을 혼합하여 바이트 코드를 실행함.
![[Java 실행 엔진 내의 인터프리터와 JIT 컴파일러.png]]
- Java [[인터프리터|interpreter]]
	- 자바 바이트 코드를 인터프리터 방식(=한 줄씩 해석, 실행하는 방식)으로 실행함.
		- 속도가 느리다는 단점.
- [[JIT 컴파일러|JIT Compiler]]
	- ![[JIT 컴파일러]]
	