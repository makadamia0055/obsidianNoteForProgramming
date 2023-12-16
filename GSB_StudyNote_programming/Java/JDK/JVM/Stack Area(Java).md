---
aliases:
  - stack
  - 스택
tags:
  - stack
  - Call_stack
  - memory
---

# Java에서 Stack area
- 말 그대로 Java에 존재하는 [[Call Stack(Memory)|call stack]] 영역.
- Java에서 Stack은 각 [[Thread(java)|스레드]]에 대한 [[메서드]] 호출과 그 메서드의 [[로컬 변수]]를 추적하고 관리하는 메모리 영역이다.
	- 즉, 자바의 실행 단위인 **스레드 별**로 프로그램의 **실행 흐름을 저장**하는 메모리/자료구조이다.

## 프로그램 실행 단위에 대한 저장 공간으로서 Stack
- 자바에서 Stack은 자바의 실행 단위([[Thread(java)]])에 대한 실행 흐름을 저장하는 곳이다.
	- 때문에 Stack은 각 [[Thread(java)]]마다 할당되고, 스레드와 생명 주기를 같이 한다. 

### Stack의 단위 : [[Stack Frame(java)|스택 프레임]]
- 자바의 스택은 [[Stack Frame(java)|스택 프레임]] 단위로 이루어져 있다. 
	- 즉, JVM 스택은 Frame을 넣고 빼는 작업을 수행하는 곳이다. [참고](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
- 스택 프레임이란?
	- ![[Stack Frame(java)]]
Java의 스택 메모리는 정적 메모리 할당 및 스레드 실행에 사용된다.





# 레퍼런스
- 