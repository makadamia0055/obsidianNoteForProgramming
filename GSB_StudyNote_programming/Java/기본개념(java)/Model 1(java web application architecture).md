---
tags:
  - architecture
  - java
  - MVC
aliases:
  - Model 1
  - MVC 1
  - JSP Model 1
---
# [[java web application architecture|자바 웹 어플리케이션 아키텍처]]에서 Model 1
- Model 1 아키텍처는 90년대 말 ~ 2000년대 초까지 자바 기반 웹 어플리케이션 개발에 사용된 아키텍처 모델을 말한다.
- Model 1, JSP Model 1이라고도 부르고, MVC 모델 이전의 모델이지만 MVC 1로 부르기도 한다.

# Model 1의 특징
- Model 1은 [[JSP]]와 [[JavaBeans]] 만을 사용해서 웹 어플리케이션을 개발한다.
- Model 1 아키텍처는 아래와 같은 구조를 가진다.
![[Model 1(java web application architecture).png]]
- Model 1에서는 [[JSP]] 페이지가 사용자의 요청을 직접 받아 처리하고, 응답을 생성한다.
- [[JavaBeans]]와 [[JSP]]는 각각 아래와 같은 기능을 지닌다.
	- [[JavaBeans]] == [[Model(MVC)]]
		- JavaBeans란? : 자바에서 Bean이란 객체를 의미하므로[^1] JavaBean은 결국 자바 객체.
		- [[Model(MVC)]] 가 "데이터베이스 연동 로직을 제공하면서, DB에서 검색한 데이터가 저장되는 자바 객체(VO, DAO 등)"을 가리키므로, 여기서 JavaBean은 데이터베이스 연동에 사용되는 자바 객체들을 가리킨다고 볼 수 있다. 
	- [[JSP]] == [[Controller(MVC)]] + [[View(MVC)]]
		- 모델 1에서는 [[JSP]] 파일이 가장 핵심 역할을 수행한다.
		- JSP 페이지가 [[Controller(MVC)]]와 [[View(MVC)]]의 기능을 모두 담당한다.
			- [[Controller(MVC)]] : 사용자 요청 처리와 관련된 자바 코드들
			- [[View(MVC)]] : 사용자에게 보여줄 화면 구성하는 코드들.
		- JSP는 Model을 사용하기 위해서
			- == 검색한 데이터를 사용자가 원하는 화면으로 제공하기 위해서
			- == View를 만들기 위해
		- 다양한 마크업을 제공한다.
		- HTML와 CSS를 통해 View를 만든다.(이 부분이랑 이 윗줄 [[정리 필요]])

### Model 1 코드 구성 예시
```
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<%@page import="com.springbook.biz.user.impl.UserDAO" %>
<%@page import="com.springbook.biz.user.UserVO" %>

<%
	// 1. 사용자 입력 정보 추출
	String id = request.getParameter("id");
	String password = request.getParameter("password");
	
	//2. DB 연동 처리
	UserVO vo = new UserVO();
	vo.setId(id);
	vo.setPassword(password);
	
	UserDAO userDAO = new UserDAO();
	UserVO user = userDAO.getUser(vo);
	
	//3. 화면 네비게이션
	if(user !=null){
		response.sendRedirect("getBoardList.jsp");
	}else{
		response.sendRedirect("login.jsp");
		      
	}


%>



<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>

</body>
</html>
```
- 상기의 상단 JSP 코드/ 하단 HTML 코드처럼 Controller 기능과 View 기능이 같은 파일에서 처리된다.
### Model 1의 단점
- Model 1 구조는 JSP 파일에서 Controller 기능과 View 기능을 모두 처리한다.
	- 결과적으로 JSP파일에 자바 코드와 마크업 관련 코드들이 뒤섞여 있다.
	- 이는 비즈니스 로직과 프레젠테이션 로직이 혼재되어 있어 역할 구분이 명확하지 못하다는 말이다.
		- 이로 인해 JSP 파일에 대한 디버깅과 유지 보수에 많은 어려움이 생길 수 밖에 없다.
	- 따라서 Model 1 구조는 적은 개발 인력으로 간단한 프로젝트를 수행할 때나 사용하기 적합하고, 엔터프라이즈 급의 복잡한 시스템에서는 부적절한 모델이다.
- 이러한 단점을 보완하기 위해 나온 모델이 [[Model 2(java web application architecture)]]
## 레퍼런스
- [[스프링 퀵스타트]]


[^1:] 사실 자바에서 Bean의 의미도 딱히 명확히 규정되어 있지는 않은 것 같다. 스프링에서는 스프링 컨테이너에 의해 생성되고 관리되는 자바 객체를 Bean이라고 정의하는데, 일단은 넘어감