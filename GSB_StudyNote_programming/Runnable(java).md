---
tags:
  - java
  - interface
  - thread
aliases:
  - Runnable
---
# Java에서 Runnable 인터페이스
- Runnable은 [[Java]]에서 멀티스레딩을 지원하기 위해 제공하는 [[Interface(java)|인터페이스]]이다.
	- Runnable은 [[Java 1.0]]부터 지원되는 핵심 [[Interface(java)|인터페이스]]이다.
		- 이후 [[Java 1.5]]에서 Runnable의 개선으로 [[Callable(java)]]이 도입되었다.

# Runnable 인터페이스의 형태
```
package java.lang;

@FunctionalInterface
public interface Runnable {
    
    public abstract void run();
}
```
- Runnable 인터페이스는 run이라는 [[Parameters|매개변수]]와 [[Return Type|반환 타입]]이 없는 [[Abstract Method(java)|추상 메소드]]가 존재한다.
- Runnable에는 함수형 인터페이스임을 제약하는 [[Functional Interface(java)#FunctionalInterface Annotation(java) Annotation|@FunctionalInterface 어노테이션]]이 선언되어 있다.
- 
## 레퍼런스
[Runnable VS Callable](https://www.baeldung.com/java-runnable-callable)
[Runnalbe -Oracle](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html)