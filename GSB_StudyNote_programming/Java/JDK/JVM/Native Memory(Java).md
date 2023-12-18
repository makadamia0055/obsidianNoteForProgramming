---
tags:
  - native_memory
  - in_working_process
aliases:
  - 네이티브 메모리
  - native memory
---
# 이 문서는 아직 깔끔하게 정리가 안됨.
- 여러 문서들에서 개념들이 상충되는데, 어떤 게 맞는지 아직 판단도 안되고, 명확한 제시가 없어서 그걸 정리하는 목적
- 각 출처 별로 어떻게 정의하는 지를 정리하는 중.

## Baeldung의 [Memory Type in JVM](https://www.baeldung.com/java-jvm-memory-types)
- [[Memory Type in JVM - Baeldung]] 번역 정리한 글
- Java/JVM에 존재하는 여러 메모리 타입, 정의, 관점 중 하나로 정의함.
- JVM 외부에 존재하면서, JVM에 의해 사용되는 공간으로 정의.
	- Java 힙의 외부이며, Off-Heap 메모리와 동의어
- native memory가 direct buffer memory를 사용할 수 있다.

## 자바에서 Native Memory란 무엇인가?
- [[understanding java native memory]]

## OS 탐구: Java 네이티브 메모리
- [[OS 탐구 - Java 네이티브 메모리]]