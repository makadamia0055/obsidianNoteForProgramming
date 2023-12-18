---
tags:
  - 번역
  - 요약
  - memory
  - java
---

### [[Native Memory(Java)|native memory]]를 찾아보다가 나온 문서

# 네이티브 메모리 부분 원문
## 4. Native Memory [link](https://www.baeldung.com/java-jvm-memory-types#native-memory)

The memory allocated outside of the Java heap and used by the JVM is called [native memory](https://www.baeldung.com/native-memory-tracking-in-jvm). It is also known by the term off-heap memory.

Since the data in native memory is outside the JVM, we need to perform serialization to read and write data. Performance depends on the buffer, serialization process, and disk space.

Additionally, due to its placement outside the JVM, it’s not freed up by the Garbage Collector.

**In native memory, the JVM stores thread stacks, internal data structures, and memory-mapped files.**

The JVM and native libraries use native memory to perform actions that can’t be accomplished entirely in Java, such as interacting with the operating system or accessing hardware resources.

### 번역
- Java 힙 외부에 할당되고 JVM에서 사용되는 메모리를 native memory라고 한다.
	- Off-Heap 메모리와 동의어이다.
- 네이티브 메모리의 데이터는 JVM 외부에 있으므로 데이터를 읽고 쓰기 위해 [[직렬화]]를 수행해야 한다.
	- 성능은 버퍼, 직렬화 프로세스 및 디스크 공간에 따라 달라진다.
- 또한 JVM 외부에 배치되기 때문에 [[Garbage Collection(java)|GC]]에 의해 해제되지 않는다.
- JVM은 native memory 내부에 스레드 스택, 내부 데이터 구조, 메모리 매핑된 파일을 저장한다.
- JVM과 native libraries는 Native memory를 사용하여 Java에서 완전히 수행할 수 없는 작업들([[OS]]나 하드웨어 리소스에 액세스 하는 것)을 수행한다.

### 요약
- JVM 외부에 있지만, JVM에 의해 사용되는 메모리.
	- Off-Heap 메모리와 동의어
- JVM 외부에 있으므로, 데이터 읽고 쓰기를 위해 직렬화를 수행해야 한다.
- 또 JVM 외부이므로 GC에 의해 해제되지 않는다.
- 스레드 스택(기존의 java stack인지 아니면 Native Method Stack인지), 내부 데이터 구조, 메모리 매핑된 파일을 저장.
- 자바에서 온전히 수행할 수 없는 작업들을 수행하는 공간.

# 다이렉트 메모리 부분 원문
## 5. Direct Memory [원문](https://www.baeldung.com/java-jvm-memory-types#direct-memory)

Direct buffer memory is allocated outside the Java heap. It represents the operating system’s native memory used by the JVM process.

**Java [NIO](https://www.baeldung.com/java-io-vs-nio) uses this memory type to write data to the network or disc in a more efficient way.**

Because direct buffers aren’t freed up by the Garbage Collector, their impact on the memory footprint of an application might not be obvious. Therefore, direct buffers should be allocated primarily to large buffers that are used in the I/O operations.

To use a direct buffer in Java, we call the _allocateDirect()_ method on [_ByteBuffer_](https://www.baeldung.com/java-bytebuffer):

```java
ByteBuffer directBuf = ByteBuffer.allocateDirect(1024);
```

When loading files into memory, Java allocates a series of _DirectByteBuffer_s using the direct memory. This way, it reduces the number of times the same bytes are copied. Buffers have a class responsible for freeing up the memory when the file is no longer needed.

We can limit the direct buffer memory size using the _–XX:MaxDirectMemorySize_ parameter:

```makefile
-XX:MaxDirectMemorySize=1024M
```

If native memory uses all the dedicated space for direct byte buffers, the _OutOfMemoryError: Direct buffer memory_ error occurs.

### 번역
- Direct Buffer Memory는 Java Heap 외부에 할당된다.
	- JVM 프로세스에서 사용하는 운영체제의 기본 메모리를 나타낸다.
- Java NIO는 이 메모리 유형을 사용하여 보다 효율적인 방식으로 네트워크 또는 디스크에 데이터를 기록한다. 
- Direct Buffers는 GC에 의해 해제되지 않으므로 Application에 끼치는 영향이 명확하지 않을 수 있다.
	- 그러므로, Direct Buffer는 주로 I/O 작업에 할당되는 큰 버퍼에 할당되어야 한다.
- Java에서 direct buffer를 사용하기 위해서는, ByteBuffer에 allocateDirect() 메소드를 호출해야 한다.
	- 예시 생략
- 파일들을 메모리에 로드할 때 Java는 direct buffer를 사용하여 일련의 DirectByteBuffer_s를 할당한다. 
	- 이렇게 하면 동일한 바이트가 복사되는 횟수가 줄어든다.
	- Buffers에는 파일이 더 이상 필요하지 않을 때 메모리 점유를 해제하는 클래스가 있다.
- -XX:MaxDirectMemorySize 파라미터를 통해 direct buffer 사이즈를 제한할 수 있다.
	- 예제 생략
- native memory가 direct byte buffers를 위한 전용 공간을 모두 사용하게 되면, the _[[OutOfMemory]]Error: Direct buffer memory_ error 가 발생하게 된다. 