---
aliases:
  - 런타임 상수 풀
  - Constant Pool
tags:
  - memory
  - jvm
  - method_area
  - const
---
# Runtime Constant Pool
- [[Method Area]]의 하위에 존재하는 메모리 영역으로, 클래스 파일의 상수 풀을 내부적으로 표현한 구현체.
- 클래스 파일이 로드 될 때 생성되며, [[클래스]]나 [[인터페이스]]의 [[바이트 코드]]가 실행되는데 필요한 Constant들을 저장함.
	- 여기서 Constant는 상수만을 의미하는 것이 아니라 Literal Constant, Type Field(Local Variable, Class Variable), Method로의 모든 [[Symbolic Reference]]까지 확장된 개념을 의미한다.[출처](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
	- 각 클래스나 인터페이스마다 각각의 런타임 상수 풀이 생성된다.
- 런타임 상수 풀은 클래스 파일의 상수 풀을 JVM에서 사용할 수 있는 형태로 표현한 것.
	- 클래스 파일의 상수 풀은 클래스나 인터페이스의 바이트 코드에서 사용되는 모든 [[Symbolic Reference]]를 한 곳에 모아둠.
	- 심볼릭 참조는 변수, 메서드, 인터페이스, 클래스의 이름 등을 포함하며, 이들은 코드가 다른 클래스에 의존하는 부분을 연결하는데 사용됨.

## Runtime Constant Pool의 주요 목적
- [[Symbolic Reference|심볼릭 참조]]를 가능케 하는 것.
- 중복을 제거하는 것.

## Runtime Constant Pool의 지원 타입
- 런타임 상수 풀은 다음과 같은 타입들을 지원한다.
	- Integer, Float : 32bit 상수(아래의 Double, Long와 더불어 대문자로 쓰여있어서 [[래퍼 클래스]]를 나타내고자 한 건가 했는데 그게 아니라 원시 타입 값이라고 함.)
	- Double, Long : 64bit 상수
	- String : 실제 바이트를 포함하는 풀의 다른 항목을 가리키는 16bit 문자열 상수
	- Class : 완전한 클래스 이름을 포함
	- Utf8 : 바이트 스트림
	- NameAndType : 콜론으로 구분된 값 쌍
		- 첫 번째 항목은 이름, 두 번째 항목은 형식을 나타냄.
	- Fieldref, Methodref, InterfaceMethodref : 점으로 구분된 값 쌍으로, 첫 번째 값은 클래스 항목을 가리키고, 두 번째 값은 NameAndType 항목을 가리킵니다.
	- boolean, short 및 byte 같은 유형들은 풀에서 정수 상수로 표시됨.
- [출처](https://www.baeldung.com/jvm-constant-pool)

## Constant Pool Resolution
- Resolution 이란 [[Symbolic Reference]] 를 [[JVM]] 에서 실제 접근할 수 있는 Direct Reference 로 변경하는 것을 말한다.


# 레퍼런스 
- [JVM의 상수 풀 소개 - Baeldung](https://www.baeldung.com/jvm-constant-pool)
- [실무면접 준비 - 4 객체지향 & JVM](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)