---
tags:
  - jvm
---
---
# JVM이란?
- Java Virtual Machine으로 자바 프로그램 실행 환경을 만들어주는 가상 머신 환경 소프트웨어.
- 자바 코드를 컴파일하여 .class 바이트 코드로 만들면 이 코드가 자바 가상 머신(=JVM) 환경에서 실행됨.
	- 이러한 구조를 통해 JVM은 자바 코드를 실행하기 위한 표준 인터페이스를 제공하고, 자바 코드가 다양한 운영체제 및 하드웨어 환경에서 동작할 수 있도록 한다.
	- 추가적으로 JVM은 [[Garbage Collector]]를 통한 메모리 관리나 스레드 관리 등을 수행함.
![[자바의 컴파일 타임 환경과 런타임 환경(jvm).png]]
- 다만, 위 그림은 [[Class Loader|클래스 로더]]가 JVM 바깥에 존재한다는 식으로 그려 놓았는데, 클래스 로더는 JVM에 속한다.
# JVM의 동작 방식
1. 자바 프로그램 실행
2. JVM이 OS로부터 메모리 할당받음
3. 자바 컴파일러([[javac.exe]])가 자바 소스코드([[.java]])를 자바 바이트 코드([[.class(java)|.class]])로 컴파일 함.
4. [[Class Loader]]를 통해 JVM [[Runtime Data Area]]로 로딩함.
5. Runtime Data Area에 로딩된 .class들이 [[Execution Engine]]을 통해 해석함.
	- 실행할 컴퓨터에 깔린 JVM에 가져다주면 JVM이 프로그램을 실행할 때 그때그때 기계어로 해석.
6. 해석된 바이트 코드는 Runtime Area의 각 영역에 배치, 수행되며, 이 과정에서 Execution Engine에 의해 GC의 작동과 스레드 동기화가 이루어짐.

# JVM의 내부 구조
![[JVM의 내부구조.png]]
- JVM은 크게 아래와 같이 구성되어 있다.
	- [[Class Loader]]
	- [[Execution Engine]]
	- [[Garbage Collector]]
	- [[Runtime Data Area(Java)|Runtime Data Area]]

## JVM의 내부 구조 : [[Class Loader]]
![[Class Loader]]

## JVM의 내부 구조 : [[Execution Engine]]
![[Execution Engine]]

## JVM의 내부 구조 : [[Garbage Collector]]
![[Garbage Collector]]


## JVM의 내부 구조 : [[Runtime Data Area]]
![[Runtime Data Area(Java)]]



# JVM의 장점과 단점
- JVM의 사용 목적은 일반적으로 다음과 같다.
	1. 플랫폼 독립성
	2. 자동화된 [[메모리 관리]](=managed memory)와 가비지 컬랙션
	3. 보안
		- 보안 모델을 적용하여 악의적인 코드의 실행을 제한
- JVM의 단점은 다음과 같다.
	1. 성능
		- JVM은 바이트 코드를 기계어로 변환하고 실행하는 과정을 겪으므로, 2번 컴파일이 일어남.
		- 때문에 일반적인 컴파일된 언어(대표적으로 C) 보다 실행 속도가 느릴 수 있음.
	2. 메모리 사용
		- JVM 자체가 추가로 메모리를 사용함
		- 가비지 컬랙션의 지연 등으로 메모리 관리가 최적화 되지 않을 수 있음.
	3. JVM 종속성으로 다른 언어와의 통합이 어려울 수 잇음.
		- 이를 위해 [[JNI]](Java Native Interface)와 같은 인터페이스를 사용해야 함.


# 더 보기
- [JVM(위키피디아)](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EA%B0%80%EC%83%81_%EB%A8%B8%EC%8B%A0)
- [JVM이란?](https://coding-factory.tistory.com/827)
- [JVM 내부 구조와 메모리 구조](https://coding-factory.tistory.com/828)
