---
tags:
  - Stack_Frame
---
# Local Variable Section
- [[Stack Frame(java)|JVM Stack Frame]]의 구성 요소로 [[메서드]]의 [[Parameters]]와 [[Local variables]]을 저장한다. 
- Local Variable Section은 0부터 시작하는 array 형 인덱스를 가진다.
	- 해당 인덱스는 [[Parameters|매개변수]] value부터 할당 된다.
	- [[Local variables|지역변수]]는 순서가 정해져 있지 않고, 사용되지 않으면 할당되지 않을 수도 있다. 
	- 매개변수나 지역변수가 [[Primitive Type(java)]]인 경우는 고정된 크기로 할당된다.
	- 하지만 String 이나 Array 같은 [[객체]]일 경우 가변 크기이므로 Reference를 갖는다.

## Local Variable Section의 구조 예시
```
class Test {  

public int testMethod (int a, char b, long c, float d, object e, double f, String g, byte h, short I, Boolean j) {

return 0; 

}
```
![[Local Variable Section의 구조 예시.png]]
- 상기의 테스트 코드로 작성한 메소드의 Local Variable Section은 그림과 같은 구조를 가진다.
	- testMethod는 10개의 매개변수를 가진다. 
	- 여기서 눈 여겨봐야 할 것
		- long와 double의 경우 array index를 두 개씩 사용한다.(칸이 더 넓음.)
		- char, byte, short, boolean 형들은 Local Variable Section에서는 모두 int 형으로 할당됨.
		- 0번 Entry의 hidden this는 선언한 적 없는 변수이지만, 0번 엔트리에 저장되어 있다.
			- Hidden this는 Heap에 있는 Class의 Instance에 대한 reference이다.
	- [출처](https://dataonair.or.kr/db-tech-reference/d-lounge/technical-data/?mod=document&uid=235941)