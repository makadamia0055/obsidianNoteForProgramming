---
tags:
  - layered_architecture
  - software_architecture_pattern
aliases:
  - CLOSED
  - 닫힌
---
# Closed
- 레이어드 아키텍처에서 '닫혀있다'(Closed)는 중요한 개념이다.
	- 이는, 계층 간 요청이 이동할 때 인접한 계층을 통과하여 전달되는 구조를 의미한다.
![[CLOSED한 계층 이동-통과의 예시.png]]
- 상기의 그림에서 Request가 이동하기 위해서는 인접한 계층을 통과하여 이동해야 한다.
	- 즉 [[Presentation Layer(layered architecture)]]에서 [[Persistence Layer]]로 요청을 보내고자 할 때, 반드시 인접한 [[Business Layer]]도 통과해야 한다는 것이다.
- 이러한 Closedness는 [[관심사의 분리]]를 위한 제약이다.
	- 반면, 이러한 Closed의 반대 개념으로 [[OPEN(layered architecture)|OPEN]]이 존재한다.


## 연관 개념
- [[layered architecture|layered architecture]]
- [[싱크홀 패턴]]
- [[OPEN(layered architecture)]]