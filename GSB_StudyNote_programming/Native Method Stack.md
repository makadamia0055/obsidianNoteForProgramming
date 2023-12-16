---
aliases:
  - 네이티브 메소드 스택
tags:
  - jvm
---

# Native Method Stack
- 자바 이외의 언어(C, C++, 어셈블리 등)로 작성된 네이티브 코드를 실행할 때 사용되는 메모리 영역으로 일반적인 C스택을 사용함.    
	- 보통 C/C++ 등의 코드를 수행하기 위한 스택을 말하며(JNI) 자바 컴파일러에 의해 변환된 자바 바이트 코드를 읽고 해석하는 역할을 하는 것이 자바 인터프리터(interpreter)
- Java 8부터 Permanent Generation이 삭제되고 대신 그 역할을 Native Method Stack의 Metaspace가 맡게 됨.
	- Native메서드 영역 내 Metaspace
	- ![[java 8 이전의 Permanent generation.png]]
    
   -  Permanent Generation
	    - Perm 영역은 보통 Class Meta 정보나 Method의 메타 정보, static 변수와 상수 정보들이 저장되는 공간으로 흔히 메타데이터 저장 영역이라고 한다.
	    - 이 영역은 Java8부터 Native Memory 영역으로 이동하였다. (기존의 Perm 영역에 존재하는 static object는 Heap 영역으로 옮겨졌다.)

이것도 [[일단 생략]]
내일 다시 쓰기