---
aliases:
  - stack
  - 스택
  - JVM Stack
tags:
  - stack
  - Call_stack
  - memory
---

# Java에서 Stack area
- 말 그대로 Java에 존재하는 [[Call Stack(Memory)|call stack]] 영역.
- Java에서 Stack은 각 [[Thread(java)|스레드]]에 대한 [[메서드]] 호출과 그 메서드의 [[로컬 변수]]를 추적하고 관리하는 메모리 영역이다.
	- 즉, 자바의 실행 단위인 **스레드 별**로 프로그램의 **실행 흐름을 저장**하는 메모리/자료구조이다.
- 자바의 스택 영역은 [[정적 메모리 할당]]이 일어난다.

## 프로그램 실행 단위에 대한 저장 공간으로서 Stack
- 자바에서 Stack은 자바의 실행 단위([[Thread(java)]])에 대한 실행 흐름을 저장하는 곳이다.
	- 때문에 Stack은 각 [[Thread(java)]]마다 할당되고, 스레드와 생명 주기를 같이 한다. 

### Stack의 단위 : [[Stack Frame(java)|스택 프레임]]
- 자바의 스택은 [[Stack Frame(java)|스택 프레임]] 단위로 이루어져 있다. 
	- 즉, JVM 스택은 Frame을 넣고 빼는 작업을 수행하는 곳이다. [참고](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
- 스택 프레임이란?
	- ![[Stack Frame(java)]]
### Stack 내의 Stack Frame 중첩
- 스택 내에서 메소드가 중첩되는 경우(메소드 내에서 또 다른 메소드 호출), 스택 프레임이 중첩되어 생성된다.
	- 정확히는 그러한 중첩 구조가 LIFO 형태인 Stack 자료구조를 통해 구현된다. 
	- 각 메서드 호출은 스택에 새 프레임 생성을 트리거하여 메서드의 지역 변수와 반환 주소를 저장합니다.
	- cf) 스택 프레임이 힙에 저장될 수 있다는 레퍼런스가 있음.
		- [The JVM Run-Time Data Areas | Baeldung](https://www.baeldung.com/java-jvm-run-time-data-areas)
		- 이거에 관해서는 나중에 더 찾아봐야 할 듯. [[일단 생략]]] 
![[Stack 내의 Stack Frame 중첩과 레퍼런스 예시.png]]
## Stack 메모리의 기타 주요 기능 및 특징
- 스택 내의 변수는 변수를 만든 메서드가 실행되는 동안에만 존재합니다.
- Stack이 가득 차면 java에서 java.lang.[[StackOverFlowError]]가 발생한다.
- 이 메모리에 대한 액세스는 힙 메모리와 비교할 때 빠릅니다.
- 이 메모리는 각 스레드가 자체 스택에서 작동하므로 스레드로부터 안전합니다.

![[Stack내의 push pop구조와 스택 프레임.png]]




# 레퍼런스
- [Java의 스택 메모리 및 힙 공간 | (주)바엘둥 (baeldung.com)](https://www.baeldung.com/java-stack-heap#stack-memory-in-java)
- [JVM에 관하여 - Part 3, Run-Time Data Area (techcourse.co.kr)](https://tecoble.techcourse.co.kr/post/2021-08-09-jvm-memory/)
- https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html