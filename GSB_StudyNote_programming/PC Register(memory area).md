---
tags:
  - PC_Register
  - memory
---
# PC Register
- [[Thread(java)]]가 생성될 때마다 생성되는 영역으로, 프로그램 카운터, 즉 현재 스레드가 실행되는 부분의 주소와 명령을 저장하고 있는 영역.
- 자바의 PC레지스터와 CPU 내의 PC 레지스터는 다름([참조](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html))
	- Register - Base 가 아닌 Stack - Base로 동작한다. 
- PC Register는 각 Thread 별로 하나 씩 존재하며, 현재 수행중인 [[JVM]] Instruction의 주소를 가지게 된다.
	- 만일 Native Method 를 수행한다면 PC Register는  Undefined 상태가 된다.
	- 이 PC Register 에 저장되는 Instruction의 주소는 Native Pointer일 수도 있고, Method Bytecode 일 수도 있다.
	- Native Method 를 수행할 때에는 JVM을 거치지 않고 [[API]]를 통해 바로 수행하게 된다.
		- 이는 Java 가 Platform에 종속 받지 않는 다는 것을 보여준다. [출처]((https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)
- PC Register의 수명 주기는 기본 스레드와 같다.

# 레퍼런스
- [Runtime Data Areas – DATA ON-AIR (dataonair.or.kr)](https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)
- [JVM 런타임 데이터 영역 | (주)바엘둥 (baeldung.com)](https://www.baeldung.com/java-jvm-run-time-data-areas)