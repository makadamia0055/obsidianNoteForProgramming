---
tags:
  - java
  - web
  - application
  - architecture
aliases:
  - 자바 웹 어플리케이션 아키텍처
  - java web application architecture
---
# # java web application architecture의 변천사
- [[Java]]의 웹 어플리케이션의 아키텍처 변천 과정을 정리한 문서
- Java Web Application Architecture는 다음과 같은 역사를 가진다.
	1. [[CGI]] : 웹 서버와 외부 프로그램 간의 표준 인터페이스로, [[Java]] 외에도 Perl, [[C 언어]] 등 다양한 언어로 개발된 어플리케이션을 실행할 수 있었다.
	2. [[Model 1(java web application architecture)|Model 1]] : [[JSP]]와 [[JavaBeans]]를 사용하여 자바 웹 서비스를 구현하는 모델([[Servlet]]는 도입 되지 않은 단계.)
	3. [[Model 2(java web application architecture)|Model 2]] : [[Servlet]]이 도입되며, 코드의 기능에 따라 [[Model(MVC)]], [[View(MVC)]], [[Controller(MVC)]]로 나누는 [[MVC]] 구조가 등장.([[Model 2(java web application architecture)|Model 2]] == [[MVC]])
	4. [[EJB]] : [[JavaEE]]의 일부로 분산 컴포넌트 아키텍처를 제공하여 기업환경에 적합한 Enterprise JavaBeans가 도입되었으나, 무겁고 복잡한 개발방식으로 사용이 어려웠음.
	5. [[Spring Framework]] : 무겁고 복잡한 [[EJB]]에 대한 대안/보완으로 [[POJO]] 기반의 경량화된 오픈소스 자바 웹 어플리케이션 프레임워크인 [[Spring Framework|Spring]]이 등장.
	6. 웹 서비스가 [[RESTful]] 아키텍처를 따르게 되었으며, [[SOA]], [[MSA]] 도입
	7. [[Cloud Native Architecture]] : 클라우드 환경에 최적화 된 클라우드 네이티브 아키텍처의 등장. 

## [[Model 1(java web application architecture)|Model 1]]
![[Model 1(java web application architecture)|Model 1]]


## [[Model 2(java web application architecture)|Model 2]]
![[Model 2(java web application architecture)|Model 2]]
## 레퍼런스
- Bing AI(GPT 4.0) / GPT 3.5