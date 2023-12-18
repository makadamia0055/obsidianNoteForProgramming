---
aliases:
  - 네이티브 메소드 스택
tags:
  - jvm
---

# Native Method Stack
- 자바 이외의 언어(C, C++, 어셈블리 등)로 작성된 네이티브 코드(네이티브 메소드)를 실행할 때 사용되는 메모리 영역으로 일반적인 C스택을 사용함. 
	- C 스택으로 불리기도 함. 
- 보통 C/C++ 등의 코드를 수행하기 위한 스택을 말하며 ([[JNI]]) 자바 컴파일러에 의해 변환된 자바 바이트 코드를 읽고 해석하는 역할을 하는 것이 자바 인터프리터(interpreter)
	- 네이티브 메소드 스택은 JVM 스택과 유사하지만, 네이티브 메소드 전용.
		- [[Stack Area(Java)|JVM Stack]]과 마찬가지로 메모리 할당 오류로 [[StackOverFlowError]] 오류나 [[OutOfMemory]] 오류가 발생할 수 있다.
- Native Method Stack의 목적은 네이티브 메소드의 실행을 추적하는 것.
- JVM에서 Native Method Stack의 크기와 메모리 블록을 처리하는 방법을 자체적으로 결정할 수 있다.
	- JVM 구현이 네이티브 메소드 호출을 지원하지 않기로 결정할 수 있다는 것.

## Native Method 작동과 Native Method Stack의 생성 과정
![[Native Method 수행과 Stack.png]]
- Application에서 Native Method를 호출하게 되면, Native Method Stack에 새로운 Stack Frame를 생성하여 Push 한다. 
	- 이는 [[JNI]]를 이용해 JVM 내부에 영향을 주지 않기 위함.
- Native Method의 수행이 끝나면 해당 Method를 호출한 Stack Frame이 아닌 새로운 Stack Frame를 하나 생성하여 작업을 수행함.
- Hotspot JVM이나 IBM JVM에서는 두 Stack 영역을 구분하지 않는다고 함.
	- 모두 Native Stack로 통합되어 있는데 이는 JVM에서 사용하는 Thread가 Native Thread인 것과 관계가 깊다. [출처](https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)



- Java 8부터 [[Permanent Generation(Java)]]이 삭제되고 대신 그 역할을 Native Method Stack의 Metaspace가 맡게 됨.
	- Native메서드 영역 내 Metaspace
	- ![[java 8 이전의 Permanent generation.png]]
    
   -  Permanent Generation
	    - Perm 영역은 보통 Class Meta 정보나 Method의 메타 정보, static 변수와 상수 정보들이 저장되는 공간으로 흔히 메타데이터 저장 영역이라고 한다.
	    - 이 영역은 Java8부터 Native Memory 영역으로 이동하였다. (기존의 Perm 영역에 존재하는 static object는 Heap 영역으로 옮겨졌다.)

이것도 [[일단 생략]]
내일 다시 쓰기