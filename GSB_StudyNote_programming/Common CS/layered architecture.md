---
aliases:
  - layered architecture
  - 레이어드 아키텍처
tags:
  - architecture
  - layered_architecture
  - software_architecture_pattern
---
# Layered Architecture란?
- 레이어드 아키텍처란 [[Software Architecture Pattern|소프트웨어 아키텍처 패턴]] 중 하나로, 소프트웨어 시스템을 관심사 별로 여러 개의 계층으로 분리한 아키텍처를 말한다.
	- 각 계층은 어플리케이션 내에서 특정 역할과 책임을 가진다.
	- 여기서 중요한 것은 구성 요소 간 관심사가 분리 되었다는 점.
- 각 계층은 추상화된 [[인터페이스]]로만 소통한다.
	- 이 때, 소통은 자신에게 인접한 하위 계층에 요청을 보내는 방식으로 진행된다.
		- 자신보다 상위 계층이나, 인접하지 않은 계층에는 요청하지 못한다.
			- 단방향 의존성 : 하위 계층은 상위 계층을 몰라야 한다. 
			- 하위 계층은 인터페이스만 제공하고 요청만 받을 뿐이다.
- 가장 일반적인 레이어드 아키텍처는 [[4 tier layered architecture|4-Tier 아키텍처]]로, 다음과 같이 구성된다.

## 레이어드 아키텍처에서 [[CLOSED(layered architecture)|CLOSED]]와 [[OPEN(layered architecture)|OPEN]]
- 레이어드 아키텍처 패턴에서는 [[CLOSED(layered architecture)]]한 구조/규율이 중시된다.
![[CLOSED(layered architecture)]]


## 레퍼런스
- [계층화된 아키텍처](https://adjh54.tistory.com/105)
- [소프트웨어 아키텍처](https://xxeol.tistory.com/26)
	- 이 블로그의 직접적인 레퍼런스는 [오 렐리 책](https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/ch01.html)인 듯 함