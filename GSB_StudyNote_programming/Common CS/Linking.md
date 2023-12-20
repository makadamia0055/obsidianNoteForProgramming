---
tags:
  - linking
  - OS
  - compile
cssclasses: 
aliases:
  - 링킹
  - Linking
---
# Linking
- 링킹은 여러 개의 코드와 데이터를 모아 연결하여 [[Memory]]에 로드될 수 있고 실행될 수 있는 한 개의 파일로 만드는 작업이다.
- 프로그램이 작성될 때, 일반적으로 여러 소스 코드 파일로 나누어져 있다.
	- 실행 가능한 프로그램을 만들기 위해서는 함수, 변수, 클래스 등으로 정의된 여러 파일들을 결합하는 과정이 필요하다. 
		- 이 파일들을 결합하는 과정이 바로 Linking 과정이다.
		- 이렇게 링킹 과정으로 만들어진 파일이 메모리에 로딩되어 실행된다.
- 링킹은 크게 두 가지 유형으로 나눌 수 있다. 
	- [[Static Linking|컴파일 타임 링킹(Compile-Time Linking)]]
	- [[Dynamic Linking|런타임 링킹(Runtime Linking)]]

# Linker
- 이런 링킹 과정을 실행하는 [[모듈]]을 Linker라고 한다.
	- 링커 역시, 실행하는 링킹 타입에 따라 아래와 같이 나눌 수 있다.
		- 컴파일 타임 링커
		- 런타임 링커

# 언어별 Linking 알고리즘
- Java : [[Linking(java)]]


## 레퍼런스
- [링커/링킹이란 무엇인가](https://live-everyday.tistory.com/67)
- [컴파일과 링킹, 런타임 로딩](https://charles098.tistory.com/102)