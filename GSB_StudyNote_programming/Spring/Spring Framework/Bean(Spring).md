---
tags: 
aliases:
  - 빈
  - 자바 빈즈
  - 자바 빈
  - Bean
---
# Spring에서 Bean이란?
- 스프링이 제어권을 가지고 직접 만들고, 관계를 부여하는 [[Object|오브젝트]]
	- [[IoC Container|스프링 컨테이너]]가 생성과 관계 설정, 사용 등을 제어해주는 [[IoC|제어의 역전]]이 적용된 [[Object|오브젝트]]
	- 이러한 관점에서 관리되는 오브젝트(Managed Object)라고 부르기도 한다.
-  자바 빈, 또는 엔터프라이즈 자바 빈에서 말하는 빈과 비슷한 오브젝트 단위의 애플리케이션 컴포넌트.

> [!warning] 주의할 점은 스프링을 사용하는 애플리케이션에서 만들어지는 모든 오브젝트가 다 빈(Bean)은 아니라는 점. 그 중에서 스프링이 직접 생성과 제어를 담당하는 오브젝트만을 빈이라고 부름.

# Bean의 [[Scope]]와 생명 주기
- Spring Bean의 스코프는 다음과 같다
	- [[Singleton Scope(Spring)]]
		- 기본 스코프
	- [[Prototype Scope(Spring)]]
	- [[Web Scope(Spring)]]

# Bean의 등록 방법
- Spring에서 Bean의 등록 방법은 빈 수동 등록 방식과 [[Component Scan(Spring)|컴포넌트 스캔]] 방식이 있다.
	- 빈 수동 등록 방식
	- 컴포넌트 스캔 방식
		- 




