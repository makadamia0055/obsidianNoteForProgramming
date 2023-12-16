---
aliases:
  - 스택 프레임
  - 프레임
  - JVM Stack Frame
tags:
  - Stack_Frame
  - Call_stack
  - stack
---

# Java에서 스택 프레임이란?
- Java의 [[Stack Frame]]으로, 자바의 [[Call Stack(Memory)|call stack]]인 [[Stack Area(Java)]]의 저장 단위.
	- [[메소드]]의 실행 흐름을 저장하기 위한 단위이므로, 메소드 마다 할당된다.
	- 메소드가 호출될 때 생성되고, 메서드가 완료될 때 소멸한다(=생명주기를 같이 한다.)
- Stack Frame은 데이터와 부분적인 결과를 저장하고, 동적인 linking을 수행하고, [[메소드]]를 위해 값을 리턴하고, [[exception|예외]]를 dispatch 하기 위해서 사용된다. 
- Stack Frame의 크기는 컴파일 시점에 결정된다.

## Stack Frame의 구조
- 스택 프레임은 [[Local Variable Section]], [[Operand Stack]], [[Frame Data]] 총 3가지 부분으로 구성되어 있다.
	- [[Local Variable Section]]
		- Method의 [[Parameters]]와 [[Local variables]]를 저장한다. 
		- ![[Local Variable Section]]
	- [[Operand Stack]]
		- Operand Stack은 프로그램 연산을 수행하면서 필요한 데이터 및 결과를 저장하는, 스레드의 작업 공간이라고 할 수 있다.
		- ![[Operand Stack]]
	- [[Frame Data]]
		- Stack Frame을 구성하고 있는 영역으로, Constant Pool Resolution 정보([[Runtime Constant Pool]]의 Pointer 정보)와 [[메서드|Method]]가 정상 종료되었을 때의 정보, 비정상 종료 했을 때의 [[exception]]관련 정보가 포함되어 있다. 
		- ![[Frame Data]]

## 레퍼런스
- [Java에서 현재 스택 추적 가져 오기 | (주)바엘둥 (baeldung.com)](https://www.baeldung.com/java-get-current-stack-trace)
- [JVM, OOP 관련 정리](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
- [Runtime Data Areas – DATA ON-AIR (dataonair.or.kr)](https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)