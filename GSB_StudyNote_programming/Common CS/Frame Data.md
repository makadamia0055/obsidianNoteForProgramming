---
tags:
  - Stack_Frame
---
# Frame Data
- [[Stack Frame]]을 구성하고 있는 영역.
- Constant Pool Resolution 정보와 [[메서드|Method]]가 정상 종료 했을 때의 정보, 비정상 종료를 했을 때 발생하는 [[exception|Exception]]정보를 가지고 있는 영역.
	- Frame Data에 저장된 [[Runtime Constant Pool|Constant Pool]] Resolution은 [[Method Area]]의 [[Runtime Constant Pool|Constant Pool]]에 대한 [[Pointer]] 정보를 말한다. 
		- [[JVM]]은 필요할 때마다 Pointer로 [[Runtime Constant Pool|Constant Pool]]에 접근한다. 보통 상수 뿐만 아니라 다른 Class를 참조하거나, Method를 접근할 경우에도 Constant Pool을  참조해야 한다.
			- 이는 Java의 모든 [[Reference]]가 [[Symbolic Reference]]인 것과 관련이 있다.
- [[메서드|Method]]가 수행이 되고 종료하는 시점에 [[Stack Area(Java)|JVM Stack]]에서 해당 [[Stack Frame(java)]]이 POP되어 사라진다. 
	- 이때 이전 Method를 찾아가야 하는데, Frame Data에는 이전에 자신을 호출한 Stack Frame의 Instruction Pointer가 들어가 있다.
- 만약 [[메서드|Method]]가 [[exception|Exception]]을 발생시키면 해당 [[exception]]을 핸들링 해줘야 하는데, 이 exception 정보가 Frame Data에 저장이 된다. 
	- [출처](https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)