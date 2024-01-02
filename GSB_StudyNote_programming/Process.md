---
tags:
  - Process
  - draft
aliases:
  - 프로세스
  - Process
---
# Process란?
- 프로세스는 컴퓨터에서 연속적으로 실행 중인 프로그램.
	- **메모리에 올라와** 실행되고 있는 프로그램의 인스턴스.
- 각 프로세스는 독립된 메모리 공간을 가지며, 서로 간섭하지 않음.
	- 이는 하나의 프로세스가 다른 프로세스의 데이터에 직접 접근할 수 없음을 의미
- 각 프로세스는 독립된 자원을 가짐.
	- 이러한 자원에는 CPU 시간, 메모리 공간, 파일, 네트워크 리소스 등이 포함.
	- 프로세스는 각각 독립된 메모리 영역(Code, Data, Stack, Heap의 구조)를 가지고 있다.
- [[IPC|프로세스 간 통신(IPC, Inter-Process Communication)]]이 필요한 경우, 별도의 메커니즘을 사용하여 정보를 교환 가능.
- 프로세스 내의 실행 단위로 [[Thread]]가 있다.

## 프로세스 메모리 구조


## 프로세스 상태 전이
![[프로세스 상태 전이도.webp]]


## 레퍼런스
- https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html
- https://blog.skby.net/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%83%81%ED%83%9C-%EC%A0%84%EC%9D%B4/
- https://s-y-130.tistory.com/322