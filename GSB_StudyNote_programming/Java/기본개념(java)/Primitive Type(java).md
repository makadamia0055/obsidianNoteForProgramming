---
aliases:
  - 원시형 데이터 타입
  - 기본형 데이터 타입
tags:
  - primitive_type
---
# 상위 개념
- [[Primitive Type]]
- [[DataType(Java)]]

# [[Java]]에서 Primitive Type(java)

- Java에서 기본형은 언어에 의해 미리 정의되고 예약된 키워드에 의해 이름이 지정된다. 
- 기본 값들은 다른 기본 값과 상태를 공유하지 않는다.
- java에서 기본형들은 기본 값을 가진다. 
	- 선언되었지만 초기화 되지 않은 필드는 컴파일러에 의해 적절한 기본 값으로 설정된다. 
- java의 기본 형은 리터럴로 할당할 수 있다.
- Java의 기본형은 객체가 아니다.
	- 때문에 [[Method(java)]]나 [[Field(java)]]를 가질 수 없다.
	- 객체가 아니기 때문에 [[null]]을 가질 수 없다.
	- 기본형은 값 자체를 저장하며, [[Reference Data Type(java)]]와 달리 [[Reference|참조]]를 저장하지 않는다.
	- 기본형은 [[Stack Area(Java)|스택]] 메모리에 저장되며, 참조형은 [[Heap(Memory)|Heap]]에 저장된다.
- Java에서는 [[String(java)]] 클래스가, [[Reference Data Type(java)|참조형]]에 속하지만 기본적인 사용은 기본형처럼 사용된다.

## Java의 기본 자료형 종류
- Java에서 기본형은 다음과 같다.
	- 논리형 : [[boolean(java)]]
	- 문자형 : [[char(java)]]
	- 정수형
		- [[byte(java)]]
		- [[short(java)]]
		- [[int(java)]]
		- [[long(java)]]
	- 부동 소수점 형
		- [[float(java)]]
		- [[double(java)]]

## 레퍼런스
- [primitive data type-오라클 공식 문서](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)
- [자바 기본 자료형 - 밸덩](https://www.baeldung.com/java-primitives)