---
aliases:
  - 심볼릭 참조
tags:
  - Symbolic
---
# Symbolic Reference 란?
- 자바에서 특정 객체를 참조할 때 Memory Address를 직접 참조하는게 아니라, 객체의 이름으로 참조한다. (예시를 자바로 든 것이지, Symbolic Reference나 Symbolic 등은 CS에서 통용되는 개념)
	- 이렇게 메모리 주소나 실제 값이 아닌 **객체의 이름**이나 **기호**로 참조하는 것을 **Symbolic Reference**라고 한다.
	- 심볼릭 레퍼런스와 대비되는, 메모리 주소나 실제 값을 통한 참조를 다이렉트 레퍼런스(Direct Reference)라고 한다. (by GPT)

## Java 에서 Symbolic Reference
- 자바에서 [[Runtime]]에 JVM은 심볼릭 참조를 실제 메모리 주소나 값으로 변환(해석)하여 메서드 호출을 가능하게 한다. 
	- 이 과정을 '링킹(Linking)' 또는 '해석(Resolution)'이라고 한다.
- 자바에서는 아래와 같은 경우에 Symbolic Reference가 일어난다. 
	- [[Runtime Constant Pool]]에서 클래스/인터페이스의 정보 저장 및 참조
	- [[클래스 파일]]의 바이트 코드에서 다른 클래스나 메서드를 참조할 때.
	- [[Reference Data Type]]도 심볼릭 참조를 사용해여 객체를 참조한다.

# 연관 개념
- [[Symbolic]]
- [[소프트 링크]]
- [[하드 링크]]
- [[Runtime Constant Pool]]


# 레퍼런스
- [심볼릭 링크 - 위키백과, 우리 모두의 백과사전 (wikipedia.org)](https://ko.wikipedia.org/wiki/%EC%8B%AC%EB%B3%BC%EB%A6%AD_%EB%A7%81%ED%81%AC)
- [실무면접 준비 - 4 객체지향 & JVM](https://imbf.github.io/interview/2021/03/02/NAVER-Practical-Interview-Preparation-4.html)
- [소프트링크, 하드링크란? (심볼릭 링크, 쉬운 설명, 개념, 정의) : 네이버 블로그 (naver.com)](https://m.blog.naver.com/jdockko1/222942172155)