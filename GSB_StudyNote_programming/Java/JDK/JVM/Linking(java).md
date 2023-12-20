---
tags:
  - linking
  - java
aliases:
  - 자바에서의 링킹
  - 링킹(java)
  - Linking(java)
---
# Java에서의 [[Linking]] 과정
- [[Java]]에서는 [[Class Loader]]에 의해 [[Dynamic Linking|동적 링킹]]이 일어난다.
	- [[Java]]는 일반적으로 [[Lazy loading]] 원칙을 따른다.
		- [[Class Loader|클래스 로더]]는 필요한 시점까지 클래스를 로딩하고 초기화하지 않고, 클래스가 처음 사용될 때 비로소 로딩과 링킹, 초기화를 실행한다. 
- Java에서는 다른 언어에 비해 링킹 과정이 비교적 간단하게 이루어진다. 
	- [[Compile(java)|자바에서의 컴파일]]에 모든 [[Class(java)|클래스]]의 코드를 [[Byte Code]]로 변환하고, 변환한 바이트 코드 파일(=[[Class File(java)|.class]])를 [[JVM]]에서 실행한다. 
		- 이 [[Class File(java)|.class]]파일은 [[JVM]]위에서 [[Dynamic Linking|동적 링킹]] 작업을 수행할 수 있도록, 라이브러리에 대한 [[Symbolic Reference]]만을 가지고 있게 된다.
- [[Java]]에서 [[Linking|링킹]]은 다음과 같은 단계에서 한 과정으로 이루어진다. 
	1. [[Compile(java)|컴파일]] : Java 소스 코드는 자바 컴파일러([[javac.exe]])를 통해 바이트 코드로 변환되며, 이 [[Byte Code]]는 [[Class File(java)|.class]]에 저장된다.
	2. 로드 : [[Class File(java)|클래스 파일]]은 [[Class Loader]]통해 [[JVM]]에 로드된다. 로드된 클래스 파일은 메모리에 클래스의 구조를 정의한다.
	3. 검증(Verification) : 로드된 클래스의 [[Byte Code]]가 유효하고 안전한지 검증한다. 이는 [[JVM]]이 악의적인 코드나 오류를 가진 코드를 실행하는 것을 방지한다.
	4. 준비(Preparation) : [[Class(java)|클래스]]의 [[정적 변수]]들이 메모리에 할당되고 기본 값으로 초기화된다.
	5. 해결(Resulution) : [[Class(java)|클래스]]나 [[Interface(java)|인터페이스]]에 대한 [[Linking|링킹]]을 수행한다. 이 단계에서는 [[Class(java)|클래스]]나 [[Interface(java)|인터페이스]]가 다른 클래스나 인터페이스를 [[Reference|참조]]할 때 필요한 정보를 수집하고 연결한다.
	6. 초기화(Initialization) : [[Class(java)|클래스]]의 [[정적 변수]]들이 프로그램 실행 중에 처음으로 값이 할당되고, 정적 초기화 블록이 실행된다. 
- Java에서는 주로 [[Dynamic Linking|동적 링킹]]에 해당하는 해결(Resolution) 단계에서 클래스나 인터페이스의 링크가 이루어진다. 
	- 이러한 방식으로 [[Java]]는 실행 중에 [[Class File(java)|클래스 파일]]을 로드하고, 필요한 [[Class(java)|클래스]] 간의 연결을 동적으로 수행한다.
	- 즉, 런타임 중에 클래스 로딩과 관련된 [[Class Loader|클래스 로더]], 링크 해결, 초기화 등의 작업이 수행된다.

## 레퍼런스
- [oop, JVM 면접준비](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
- GPT 3.5