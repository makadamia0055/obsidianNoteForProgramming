---
tags:
  - variable
  - java
aliases:
  - 변수
  - Variable
---
# [[Java]]에서 Variable(java)

## [[Java]]에 정의된 변수의 종류
- 자바는 다음과 같은 번수의 종류를 정의한다.
	- [[Instance Variables(java)]] (=Non-Static Fields)
	- [[Class Variables(java)]] (=Static Fields)
	- [[Local variables(java)]]
	- [[Parameters(java)]]
Having said that, the remainder of this tutorial uses the following general guidelines when discussing fields and variables. If we are talking about "fields in general" (excluding local variables and parameters), we may simply say "fields". If the discussion applies to "all of the above", we may simply say "variables". If the context calls for a distinction, we will use specific terms (static field, local variables, etc.) as appropriate. You may also occasionally see the term "member" used as well. A type's fields, methods, and nested types are collectively called its _members_.

## Java의 변수 네이밍 규칙
모든 프로그래밍 언어에는 사용할 수 있는 이름 종류에 대한 고유한 규칙과 규칙이 있으며 Java 프로그래밍 언어도 다르지 않습니다. 변수 이름 지정 규칙과 규칙은 다음과 같이 요약할 수 있습니다.

- 변수 이름은 대소문자를 구분합니다. `$`변수 이름은 모든 법적 식별자(문자, 달러 기호 " " 또는 밑줄 문자 " " 로 시작하는 무제한 길이의 유니코드 문자 및 숫자 시퀀스)일 수 있습니다 `_`. `$`그러나 규칙은 항상 " " 또는 " " 가 아닌 문자로 변수 이름을 시작하는 것입니다 `_`. 또한 규칙에 따라 달러 기호 문자는 전혀 사용되지 않습니다. 자동 생성된 이름에 달러 기호가 포함되는 상황이 있을 수 있지만 변수 이름에는 항상 달러 기호를 사용하지 않아야 합니다. 밑줄 문자에도 비슷한 규칙이 있습니다. 변수 이름을 " `_`"로 시작하는 것은 기술적으로 합법적이지만 이 방식은 권장되지 않습니다. 공백은 허용되지 않습니다.
- 후속 문자는 문자, 숫자, 달러 기호 또는 밑줄 문자일 수 있습니다. 이 규칙에도 관례(및 상식)가 적용됩니다. 변수 이름을 선택할 때 난해한 약어 대신 전체 단어를 사용하세요. 이렇게 하면 코드를 더 쉽게 읽고 이해할 수 있습니다. 대부분의 경우 코드를 자체적으로 문서화할 수도 있습니다. `cadence`예를 들어 , `speed`, 및 이라는 이름의 필드는 , 및 와 `gear`같은 축약형 버전보다 훨씬 더 직관적입니다 . 또한 선택한 이름은 [키워드나 예약어가](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html) 아니어야 합니다 .`s``c``g`[](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html)
- 선택한 이름이 한 단어로만 구성된 경우 해당 단어의 철자를 모두 소문자로 입력하세요. 두 개 이상의 단어로 구성된 경우 각 후속 단어의 첫 글자를 대문자로 표시합니다. 이름 `gearRatio`과 `currentGear`이 규칙의 대표적인 예입니다. 변수가 와 같은 상수 값을 저장하는 경우 `static final int NUM_GEARS = 6`규칙이 약간 변경되어 모든 문자를 대문자로 표시하고 후속 단어를 밑줄 문자로 구분합니다. 관례적으로 밑줄 문자는 다른 곳에서는 사용되지 않습니다.
- [[정리 필요]]

## 레퍼런스
- [변수 - 오라클 공식문서](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/variables.html)