---
tags:
  - memory
aliases:
  - 메소드 영역
  - 메서드 영역
  - Static Area
  - Class Area
---
# Method Area
- 메소드 영역은 JVM의 메모리 영역인 [[Runtime Data Area(Java)|Runtime Data Area]]의 구성 요소 중 하나로, 런타임에 객체를 생성하고 메서드를 호출하는 데 필요한 [[클래스]]와 [[인터페이스]]의 정의를 저장하는 메모리 영역. 
	- 이러한 정의(정보)로는 다음과 같은 것이 있다.
		- 인스턴스 생성을 위한 객체 구조
		- 생성자
		- 필드
	- 또한, [[Runtime Constant Pool|런타임 상수 풀]]과 [[정적 변수]], [[메서드 데이터]]같은 클래스 데이터들도 이곳에서 관리된다. 
	- 이 영역은 JVM 당 하나만 생성되며, 인스턴스 생성에 필요한 정보도 존재하기 때문에 JVM의 모든 스레드들이 공유하는 영역이다. 
- Class Area, Static Area 등으로도 불린다.
	- 클래스 파일의 바이트 코드가 로드 되는 곳이기 때문에.
		- 이렇게 Method Area에 바이트 코드가 올라가는 것을 클래스 로딩이라고 한다.


## Method Area의 저장 형식
- ![[메소드 영역의 클래스 저장 형식.png]]
- 상기 그림에서 [[Runtime Constant Pool|런타임 상수 풀]]이 클래스마다 할당되는 것을 볼 수 있다.

## 레퍼런스 
- [Method Area에 대해](https://blog.wanzargen.me/16)
- 