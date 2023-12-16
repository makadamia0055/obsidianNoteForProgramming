---
tags:
  - Stack_Frame
---
# Operand Stack
- JVM (스레드)의 작업 공간
- 메소드 내 연산을 위해 바이트 코드 명령문들이 들어있는 곳으로, 프로그램 연산을 수행하면서 필요한 데이터 및 결과를 저장하는 곳.
- 작동 방식
	- 하나의 Instruction이 연산을 위해 Operand Stack에 값을 넣으면 다음 Instruction에서는 이 값을 빼서 사용하게 된다.
	- 연산의 결과 역시 Operand Stack에 저장된다. 
- Operand Stack 역시 Array로 구성되어 있으며, Stack의 구조로 Push, Pop 작업을 수행한다.
	- [출처](https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)