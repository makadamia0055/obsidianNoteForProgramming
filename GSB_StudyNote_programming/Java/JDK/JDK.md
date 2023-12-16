---
tags:
  - jdk
  - jvm
  - java
  - architecture
---
---
# JDK 란?
- JDK = Java Development Kit
	- 개발자들이 자바로 개발하는 데 사용하는 SDK(Softward Development Kit)\
# JDK의 구성
![[JDK의 구성.png]]
- JDK는 [[JRE]]와 [[Java Development Tools]]로 구성되고, JRE는 다시 [[JVM]]과 [[Java Class Library]]로 구성된다.

## JDK의 구성 : JRE란?
![[JRE]]

## JDK와 JRE의 기능
- JDK : Java 프로그램을 개발하기 위해 필요한 소프트웨어 패키지.
	- JRE를 포함하고 있다.
- JRE : Java 프로그램을 사용하기 위해 필요한 소프트웨어 패키지

## JDK 디렉토리 구성 요소
![[JDK 디렉토리 구성 요소.png]]
- bin : 자바 개발, 실행에 필요한 도구와 유틸리티 명령
- include : 네이티브 코드 프로그래밍에 필요한 C언어 헤더 파일
- lib : 실행 시간에 필요한 라이브러리 클래스들
- 상기 이미지에서 jdk/bin과 jdk/jre/bin 의 차이는?
	- JDK (Java Development Kit) 디렉토리 내에 JRE (Java Runtime Environment)가 있는 이유는 다양한 개발 및 실행 환경을 지원하기 위해서입니다.
		1. **JDK/bin:**
			- `javac`: Java 소스 코드를 컴파일하는 데 사용되는 컴파일러가 여기에 포함되어 있습니다.
			- `java`: Java 프로그램을 실행하는 데 사용되는 실행기도 여기에 포함되어 있습니다.
		2. **JDK/jre/bin:**
			- `java`: JDK 내에 포함된 JRE의 실행기가 여기에도 포함되어 있습니다.
			- JDK에는 개발 도구와 함께 실행 환경이 필요하므로, JRE도 함께 제공됩니다.
		- 여기서 주목할 점은 JDK에는 개발 도구와 함께 실행 환경도 포함되어 있기 때문에, JDK의 `bin` 디렉토리에는 컴파일러와 실행기가 함께 제공됩니다. 하지만 몇몇 배포판에서는 JDK 내에 포함된 JRE의 경량화된 버전을 `jre` 디렉토리로 따로 제공하기도 합니다.
		- JDK와 JRE를 나누는 이유 중 하나는 개발자가 개발할 때는 JDK를 사용하고, 사용자가 프로그램을 실행할 때는 JRE만 있으면 되기 때문입니다. 개발자가 자바 애플리케이션을 배포할 때, JDK의 `bin` 디렉토리에 있는 실행 파일들은 필요하지 않을 수 있습니다. 따라서 JRE를 별도로 분리하여 사용자가 필요한 최소한의 실행 환경만을 제공할 수 있습니다.

### bin 디렉터리에 들어 있는 개발 프로그램
![[jdk bin 디렉토리 구성요소.png]]
- [[javac.exe]] : 자바 컴파일러로 자바 소스를 바이트 코드로 컴파일
- [[java.exe]] : 자바 인터프리터. 컴파일러가 생성한 바이트 코드를 해석하고 실행
- javadoc.exe : 자바 소스로부터 HTML 형식의 API 도큐먼트 생성
- jar.exe : 자바 클래스 파일을 압축한 자바 아카이브 파일(.jar)을 생성, 관리하는 압축 프로그램.
- jmod.exe : 자바의 모듈 파일([[.jmd]])을 만들거나 모듈 파일의 내용 출력
- jlink.exe : 응용프로그램에 맞춘 맞춤형 JRE 생성하는 프로그램.
- jdb.exe : 자바 응용프로그램의 실행 중 오류를 찾는 데 사용하는 디버거
- javap.exe : 역어셈블러. 컴파일된 클래스 파일을 원래의 소스로 변환