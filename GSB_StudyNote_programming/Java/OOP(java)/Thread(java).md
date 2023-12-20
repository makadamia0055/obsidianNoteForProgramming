---
tags:
  - thread
aliases:
  - 스레드
  - 쓰레드
---
# Java에서의 Thread
- Java에서 스레드는 [[JVM]]에 의해 생성되며, 각 스레드는 자신만의 [[Stack Area(Java)]]을 가진다.
	- 이 [[Method Area|Static Area]]은 메서드 호출과 로컬 변수 등 스레드의 실행 경로를 추적하는데 사용된다.
- 그러나 스레드 자체가 [[JVM]]의 [[Runtime Data Area(Java)|Runtime Data Area]]에 생성되는 것은 아니다.
	- 스레드는 [[운영체제]] 수준에서 관리되며, [[JVM]]은 이러한 스레드를 [[추상화]]하여 자바 애플리케이션에서 사용할 수 있게 한다. [출처](https://velog.io/@qlgks1/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-%EC%9E%90%EB%B0%94-%EC%A4%91%EA%B8%89-%EC%93%B0%EB%A0%88%EB%93%9C)
		- 따라서, 스레드는 JVM이나 자바 애플리케이션의 실행 흐름을 나타내는 독립적인 단위로 볼 수 있다.
- 스레드가 생성되면, JVM은 프로그램의 시작점인 [[main()]] 메서드를 찾아 메인 스레드를 생성하고, 메인 스레드의 run() 메서드를 통해 해당 main() 메서드를 실행한다.
	- 이후 필요에 따라 추가적인 스레드를 생성하여 병렬 처리를 수행할 수 있다.


## Java에서 Thread를 구현하기 위한 [[Interface(java)|인터페이스]]
- Java에서는 멀티스레드 작업을 구현하기 위한 [[Interface(java)|인터페이스]]로 [[Runnable(java)]]과 [[Callable(java)]]를 제공한다.


[[일단 생략]]

### [[Runnable(java)]]
![[Runnable(java)]]


# 참조
- [[프로그래머스] 자바 중급 - 쓰레드 (velog.io)](https://velog.io/@qlgks1/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-%EC%9E%90%EB%B0%94-%EC%A4%91%EA%B8%89-%EC%93%B0%EB%A0%88%EB%93%9C)
- [JAVA 기초 | 자바 기초 스레드 thread, join, Synchronized : 네이버 블로그 (naver.com)](https://m.blog.naver.com/ghdalswl77/221868897180)
- [쓰레드, 동기화, 풀, Runnable, Callable, Future](https://jh-labs.tistory.com/269)