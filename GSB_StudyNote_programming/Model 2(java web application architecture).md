---
tags:
  - architecture
  - java
  - MVC
aliases:
  - Model 2
  - JSP Model 2
  - MVC2
---
# 주의
Model 2는 [[MVC]]와 동일한 개념으로, 이 문서는 Model 1에서 Model 2의 분화과정을 다루기 위해 작성하였다.(이후 MVC와 통합할 지 모름.)

# [[java web application architecture|자바 웹 어플리케이션 아키텍처]]에서 Model 2
- Model 2 아키텍처는 자바 웹 어플리케이션 아키텍처 모델로, 기존의 [[Model 1(java web application architecture)|Model 1]]의 단점을 보완하기 위해 등장했다.
	- [[Model 1(java web application architecture)|Model 1]]의 구조에서는 자바 로직과 화면 디자인이 같은 파일에 통합되어 있어, 유지 보수가 어렵다.
		- 디자이너, 개발자 모두 수정할 자바 로직과 디자인 태그를 찾는 것부터 어려움.

## Model 2의 아키텍처 구조
- [[Controller(MVC)]] 의 도입
	- 기존에 [[JSP]]에서 클라이언트로부터의 요청을 담당하는 [[Controller(MVC)]] 기능을 [[Servlet]]이 담당하게 됨.
		- 따라서 기존 [[Model 1(java web application architecture)|Model 1]]아키텍처로 개발한 프로그램에서 JSP 파일의 자바 코드만 Controller로 옮기면 Model 2 아키텍처가 된다.
	- 결과적으로 [[Controller(MVC)]] 코드가 사라진 [[JSP]]파일에는 View와 관련된 코드만 남게 된다.
 ![[Model 2(java web application architecture).png]]
 - 즉, Model 2 부터는 [[Model(MVC)]], [[View(MVC)]], [[Controller(MVC)]]가 각각 영속성을 담당하는 [[Java]] Logic Class, [[JSP]], [[Servlet]]로 분리되어 관리된다.
	 - 이러한 점에서 [[Model 1(java web application architecture)|Model 1]]의 단점이 해결됨
		 - 디자이너는 [[JSP]] 파일을, 자바 개발자는 [[Controller(MVC)]]와 [[Model(MVC)]]만 관리하면 된다.
	- Model 2에서 MVC라는 개념이 도입되었으므로 Model 2는 [[MVC]]와 동일한 개념이라고 할 수 있다.

## 레퍼런스
- [[스프링 퀵스타트]]