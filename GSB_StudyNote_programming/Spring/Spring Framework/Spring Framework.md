---
tags:
  - Spring
  - framework
aliases:
  - 스프링
  - Spring
  - 스프링 프레임워크
  - Spring Framework
---
# Spring Framework란?
- 스프링(Spring)은 Java 백엔드 개발에 있어 핵심적인 프레임워크로, 엔터프라이즈용 [[Java]] 어플리케이션 개발을 편하게 할 수 있게 해주는 오픈소스 경량급 어플리케이션 프레임워크이다.
- 스프링의 주요 특징은 다음과 같다.
	- [[POJO]] 프로그래밍 지향
		- 스프링에서는 POJO라는 핵심 가치를 아래와 같은 개념들로 구현한다.
			- [[IoC]]
			- [[서비스 추상화]]
			- [[AOP]]
## 공통 프로그래밍 모델 -[[POJO]]와 [[IoC]], [[서비스 추상화]], [[AOP]]



## Spring? Spring Framework?
- Spring과 Spring Framework라는 용어는 거의 같은 의미로 사용되지만, 그것이 구분되어 사용될 때는 각각 다른 영역을 가리키기도 한다.
	- Spring이라는 용어는 전체 스프링 프로젝트에 대한 명명으로 사용되며, 그 하위에 여러 프로젝트를 포함하는 큰 생태계로서의 의미를 가진다.
	- 반면, Spring Framework라는 용어는 전체 스프링 프로젝트 중 스프링의 핵심 프로젝트(Core Project)로, 스프링의 기본적인 기능을 제공하는 프레임워크를 의미한다. 

## 전체 Spring의 프로젝트 목록
- 스프링 프레임워크
- [[Spring Boot]]
- [[Spring Data]]
- [[Spring Clould]]
- [[Spring Security]]
- [[Spring Session]]
- [[Spring Intergration]]
- [[Spring Batch]]
	- 이외에도 다른 스프링 프로젝트들이 존재한다. 
	- [전체 스프링 프로젝트 목록 - 스프링 공식 사이트](https://spring.io/projects/)


## Spring Framework의 구성
- Spring Framework는 모듈로 이루어져 있다. 
	- 어플리케이션은 필요한 모듈을 선택할 수 있다.
	- 그 중심에는 구성 모델과 종속성 주입 메커니즘을 포함하는 코어 컨테이너의 모듈이 있다. 
- 그 외에도 스프링 프레임워크는 메시징, 트랜잭션 데이터 및 지속성, 웹을 포함한 다양한 어플리케이션 아키텍처에 대한 기본 지원을 제공한다.
- 또한 [[Servlet]] 기반 [[MVC(Spring)|Spring MVC 웹 프레임워크]]와 동시에 Spring WebFlux 반응형 웹 프레임워크도 포함되어 있다. 

## Spring의 역사와 Spring Framework
- Spring은 초기 [[J2EE]] 사양의 복잡성에 대한 대응으로 2003년 등장했다.
	- 일부에서는 Java EE/그 후속 버전인 Jakarta EE가 Spring과 경쟁 관계에 있다고 생각하지만, 실제로는 상호 보완적.

## Spring의 디자인 철학
- Spring Framework의 기본 디자인 철학은 다음과 같다.(스프링 공홈 출처)
	- 모든 수준에서 선택권을 제공하라.
		- Spring을 사용하면 디자인 결정을 가능한 한 늦게 연기할 수 있다.
	- 다양한 관점을 수용하라.
		- Spring은 유연성을 수용하며 작업 수행 방법에 대해 고집을 부리지 않는다.
		- 다양한 관점으로 광범위한 어플리케이션 요구사항을 지원한다.
	- 이전 버전과 강력한 호환성을 유지하라.
		- Spring의 발전은 버전 간 주요 변경 사항을 거의 강제하지 않도록 신중하게 관리된다.
		- Spring은 Spring에 의존하는 어플리케이션과 라이브러리의 유지 관리를 용이하게 하기 위해 신중하게 선택된 다양한 JDK 버전과 타사 라이브러리를 지원한다.
	- API 디자인에 신경쓰라
		- Spring은 직관적이고 여러 버전과 수년에 걸쳐 유지되는 API를 만들기 위해 노력한다.
	- 코드 품질에 대한 높은 기준을 설정하라.
		- 스프링은 패키지 간 순환 종속성이 없는 깔끔한 코드 구조를 주장할 수 있는 몇 안되는 프로젝트 중 하나이다.

# Spring Framework의 모듈 구조
![[Spring Framework 3.2의 모듈 구조.png]]


# 스프링 프레임워크의 구동 순서 
[[Spring/Spring Framework/Spring Framework의 구동 순서]]


## 레퍼런스
- [[토비의 스프링 3.1]]
- [스프링 6.0 공식 문서](https://docs.spring.io/spring-framework/reference/overview.html)
- [스프링 3.2 docs](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/overview.html)
	- [스프링 3.2 전체 Index](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/index.html)