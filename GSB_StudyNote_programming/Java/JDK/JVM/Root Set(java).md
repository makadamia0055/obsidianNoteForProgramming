---
tags:
  - memory
  - GC
aliases:
  - Root Set
---
# Root Set란?
- [[Garbage Collector|GC]]를 위해 Reachable Reference 여부를 파악하려면 항상 **유효**한 **최초**의 **참조**가 있어야 한다.
	- 이를 객체 참조의 **Root Set**이라고 한다.
- [[Garbage Collector|GC]]는 Root Set에서 시작하여 참조되는 모든 객체들을 추적하고, [[Garbage Collector|GC]]의 대상이 되는 객체들을 판별한다.

## Root Set의 구분
- Root set은 아래와 같이 구분한다.
	- [[Stack Area(Java)|stack]]의 참조 정보
		- [[Local Variable Section]]과 [[Operand Stack]]의 참조
	- [[JVM]] [[Method Area]]의 [[Runtime Constant Pool|Constant Pool]]에서의 참조([[정적 변수]]에 의한 참조)
	- [[Native Method Stack]]으로 넘겨진 [[Object(java)|Object]]의 [[Reference]] 참조
- 이 세 가지 [[Reference]] 정보에 의해 직간접적으로 참조되고 있다면,  **Reachable Object**이고, 그렇지 않다면 모두 **Garbage Object(Unreachable Object)이다.


## 레퍼런스
[OOP, JVM 면접준비](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)