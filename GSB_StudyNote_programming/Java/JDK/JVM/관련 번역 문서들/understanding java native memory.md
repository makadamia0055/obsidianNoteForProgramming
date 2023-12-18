---
tags:
  - 번역
  - native_memory
  - java
  - 요약
---
### 원 문서
[understanding java native memory](http://www.trevorsimonton.com/blog/2020/09/09/java-native-memory.html)

### 원 문서 중 "what is native memory"
### what is native memory?

The short answer is that “native memory” basically refers to the memory used by the JVM process itself. This includes the heap, anything allocated from the JNI native code, the data structures used to manage the heap, the stack memory, class definitions, and anything else the JVM might need to function.

You could say that “native memory” also includes memory used by other processes and by the operating system itself.

At the end of the day, the JVM is just a regular process. It needs memory to function, and in the Java world this seems to be called “native memory.”

I feel like the rest of the world would just call this “memory.”

### 요약
- native memory는 기본적으로 [[JVM]] 프로세스에 의해 사용되는 메모리 공간 그 자체를 가리킨다.
	- 여기에는 [[Heap Area(Java)|Heap]]과 [[JNI]] 네이티브 코드에서 할당된 모든 것, 힙을 관리하기 위해 사용되는 데이터 구조들, [[Stack Area(Java)|stack]], 클래스 정의 및 JVM에 필요한 기타 모든 것들이 포함된다.
- 또한 native memory는 다른 프로세스에 의해 점유된 메모리나 "[[OS]]" 자체에 의해 점유된 메모리도 포함한다고 할 수 있다.
- 즉, native memory는 그냥 'memory'라고 불리는 영역이다.
	- JVM도 결국 단지 일반적인 프로세스일 뿐이다. 
	- JVM이 기능하기 위해서는 메모리가 필요하며, Java에서는 이를 [[Native Memory(Java)|native memory]]라고 부르는 것이다. 
- 이 문서에서는 native memory는 JVM에서 사용되는 메모리 공간 그 자체를 통칭하여 부르는 개념으로 보는 듯.

[[일단 생략]]
나중에 필요가 생기면 더 보충하기로.