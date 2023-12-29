---
tags:
  - MVC
  - architecture
aliases:
  - MVC
  - Model-View-Controller
---
# MVC란?
- MVC란 [[Model(MVC)]] - [[View(MVC)]] - [[Controller(MVC)]] 의 세 가지 구성 요소로 정의된 소프트웨어 디자인 패턴이다. 
- 이 패턴을 사용하면, 사용자 인터페이스로부터 비즈니스 로직을 분리하여 애플리케이션의 시각적 요소나 그 이면에서 실행되는 비즈니스 로직을 서로 영향 없이 쉽게 고칠 수 있는 어플리케이션을 만들 수 있다. 
## M-V-C의 각 요소와 요소 간의 상호 작용
![[MVC 각 요소의 상호작용 예시.png]]
-  MVC 패턴의 각 요소는 아래와 같다.
	- 어플리케이션의 정보를 나타내는 [[Model(MVC)]]
	- 사용자 인터페이스 요소를 나타내는 [[View(MVC)]]
	- 데이터와 비즈니스 로직 사이의 상호 동작을 관리하는 [[Controller(MVC)]]
- 각 요소들은 아래와 같이 상호작용 한다.
	 - **컨트롤러**는 모델에 명령을 보냄으로써 모델의 상태를 변경할 수 있다. (예: 워드 프로세서에서 문서를 편집하는 것) 또, 컨트롤러가 관련된 뷰에 명령을 보냄으로써 모델의 표시 방법을 바꿀 수 있다. (문서를 스크롤하는 것)
	- **모델**은 모델의 상태에 변화가 있을 때 컨트롤러와 뷰에 이를 통보한다. 이와 같은 통보를 통해서 뷰는 최신의 결과를 보여줄 수 있고, 컨트롤러는 모델의 변화에 따른 적용 가능한 명령을 추가·제거·수정할 수 있다. 어떤 MVC 구현에서는 통보 대신 뷰나 컨트롤러가 직접 모델의 상태를 [읽어 오기](https://ko.wikipedia.org/wiki/%ED%8F%B4%EB%A7%81_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99) "폴링 (컴퓨터 과학)")도 한다.
	- **뷰**는 사용자가 볼 결과물을 생성하기 위해 모델로부터 정보를 얻어 온다.

# Web 에서의 MVC
![[웹 어플리케이션에서 일반적인 mvc 구성요소 다이어그램.png]]
## 자바 웹 어플리케이션 아키텍처 모델에서의 MVC
- [[java web application architecture]]에도 [[Model 2(java web application architecture)|MVC 패턴]]은 도입되었다.
- Spring에서의 [[MVC(Spring)|MVC]]

## 레퍼런스
- [모델-뷰-컨트롤러 - 위키피디아](https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC)
- [MVC 패턴 - MDN](https://developer.mozilla.org/ko/docs/Glossary/MVC)