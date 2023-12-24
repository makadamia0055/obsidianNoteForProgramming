---
tags:
  - concept_mapping
aliases:
---
# Spring Legacy의 개념 계층화 트리

- [[Spring Legacy]]
	- [[XML]] 기반 설정
		- [[applicationContext.xml]]
		- [[Bean(Spring)|Bean]] 설정
			- id와 name 속성
			- property 태그
			- constructor-arg 태그
	- [[AOP]] 설정
		- [[XML]]을 이용한 [[AOP]] 설정
		- aop:aspect 태그
		- aop:pointcut 태그, aop:advice 태그
	- [[Transaction]] 설정
		- tx:advice 태그
		- tx:attribute 태그
	- [[MVC(Spring)|MVC]] 설정
		- [[Dispatcher Servlet]] 설정
		- [[ModelAndView]] 객체
		- [[ViewResolver]] 설정
	- 데이터 액세스 설정
		- [[DataSource]] 설정
		- [[JDBC Template]] 설정
		- [[Hibernate]] 설정
	- 보안 설정
		- security:authentication-manager 설정
		- security:http 태그
	- 메시징 설정
		- jms:listener-container 태그
		- jms:listener 태그


## 레퍼런스
- GPT 3.5