---
tags:
  - GC
  - memory
aliases:
  - Mark-And-Sweep
---
# Mark And Sweep란?
- 마크앤스윕은 여러 [[Garbage Collector|GC]] 들에서 사용하는 내부 알고리즘으로, 가비지 컬렉션의 대상이 될 대상을 식별(Mark)하고 제거(Sweep)하며, 이후 객체가 제거된 파편화된 메모리 영역을 압축(Compaction)하는 작업을 수행하는 알고리즘이다.
	- Mark And Sweep는 [[포인터 추적 방식]]에 해당한다.
![[Mark And Sweep 예시.png]]

- 즉 아래와 같은 단계로 이루어져 있다.
	- Mark 과정  
		- 먼저 [[Root Set(java)|Root Set]]으로부터 [[그래프 순회]]를 통해 연결된 객체들을 찾아내어 각각 어떤 객체를 참조하고 있는지 찾아서 마킹한다.
	- Sweep 과정
		- mark 과정을 통해 선별한 [[Root Set(java)|Unreachable]]한 객체들을 Heap에서 제거한다.
	- Compact 과정
		- Sweep 이후 분산된 객체들을 [[Heap(Memory)|Heap]]의 시작 주소로 모아 메모리가 할당된 부분과 그렇지 않은 부분으로 압축한다.
			- Compact 과정은 가비지 컬렉터 종류에 따라 하지 않는 경우도 있다. 
- 이렇게 Mark And Sweep 방식을 사용하면 루트로부터 연결이 끊긴 [[순환 참조]]되는 객체를 모두 지울 수 있다. 
![[Mark And Sweep GIF 예시.gif]]

## 레퍼런스
[GC 원리와 알고리즘 총정리- inpa blog](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
[GC - coding factory](https://coding-factory.tistory.com/829)