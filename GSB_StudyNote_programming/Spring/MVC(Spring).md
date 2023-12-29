---
tags:
  - MVC
  - Spring
aliases:
  - MVC
---
# 상위문서
- [[MVC]]

스프링의 MVC 구조는 다음과 같은 세 가지 주요 요소로 구성됩니다:

1. **Model**: Model은 **애플리케이션의 상태**와 **비즈니스 로직**을 담당합니다. 비즈니스 로직을 수행하고 데이터를 처리하는데 사용되며, 사용자 인터페이스나 데이터베이스와 직접적으로 상호작용하지 않습니다. Model은 **도메인 객체, 서비스, 리포지토리, DTO**(Data Transfer Object) 등으로 구성됩니다.
2. **View**: View는 사용자에게 **정보를 표시하는 역할**을 합니다. 일반적으로 웹 애플리케이션에서는 HTML, JSP, Thymeleaf 등의 템플릿 엔진을 사용하여 View를 구현합니다. View는 Model에 저장된 데이터를 사용하여 사용자에게 정보를 표시하고, 사용자로부터 입력을 받을 수 있습니다.
3. **Controller**: Controller는 **사용자의 요청을 처리**하고, **적절한 비즈니스 로직을 호출**하여 데이터를 **가공**한 후, **View에 전달하는 역할**을 합니다. 사용자 요청을 매핑하여 해당하는 비즈니스 로직을 실행하고, 처리 결과를 **Model에 저장하여 View에 전달**합니다.(=핸들러 라고도 한다)

스프링의 MVC 구조에서는 클라이언트(웹 브라우저)에서 HTTP 요청이 발생하면 DispatcherServlet이 해당 요청을 받아들입니다. DispatcherServlet은 요청을 처리하기 위해 **HandlerMapping**과 HandlerAdapter를 사용하여 적절한 컨트롤러를 찾고, 해당 컨트롤러에서 비즈니스 로직을 실행합니다. 컨트롤러가 실행된 결과는 Model에 저장되고, View에 전달되어 클라이언트에게 응답으로 제공됩니다.
[[정리 필요]]

[[HandlerMapping]]
[[HandlerAdapter]]
[[ViewResolver]]

[[Model 1(java web application architecture)]]
[[Model 2(java web application architecture)]]


## 레퍼런스
- [DispatcherServlet, Model 1, Model 2, FrontController](https://ride-dev.tistory.com/55)