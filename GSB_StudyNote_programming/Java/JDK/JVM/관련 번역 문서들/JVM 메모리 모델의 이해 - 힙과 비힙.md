---
tags:
  - 번역
  - 요약
  - 웹문서
---
[원문](https://betterprogramming.pub/understanding-the-jvm-memory-model-heap-vs-non-heap-c14aa6fa703e)

# 원문 전문

The JVM is a beloved concept that every Computer Science undergrad knows by heart. It’s been with us since 1994, and although it went through quite a few iterations since, it’s still holding strong.

## Why should you care about the JVM memory model?

Whether you’re using Java, Kotlin, Scala, Groovy, or even Neo4j for your graph database, you’ve got the JVM working behind the scenes.

So, to have a stronger command in your niche, it makes sense to have some understanding of how it works, and particularly how its memory model is built, unless you happen to be a fan of OOM errors.

Understanding how memory is managed is especially relevant nowadays, as many of us are working with microservices that run inside containers that have a limited memory capacity. If you want to reduce costs and retain your application’s performance, you need to efficiently manage your container’s memory.

Now, you might ask — I’m not a DevOps engineer, how is this relevant for me?  
Well, first of all, I personally believe that having a working knowledge of basic concepts, even outside your realm of expertise, is always a good idea. It allows you to be more useful and versatile, and sometimes even come up with creative ideas — connecting the dots between seemingly separate fields. Second of all, it allows you to manage your application better, as you’ll be able to understand the inner workings of your application, allowing you to spot potential issues or even potential areas for improvement.

So, what are we waiting for? Let’s dive in!  
One important distinction to make when it comes to JVM memory is where the memory is stored, as it affects various aspects of the application.

We can roughly divide the JVM memory into two parts:

- Heap Memory
- Non-Heap Memory

Let’s start with what most people are already familiar with — Heap memory.

## JVM Heap Memory

Broadly speaking, the JVM heap consists of Objects (and arrays).  
Once the JVM starts up, the heap is created with an initial size and a maximum size it can grow to. For example:

-Xms256m // An initial heap size of 256 Megabytes  
-Xmx2g // A maximum heap size of 2 Gigabytes 

Every time you create a new object, the JVM allocates memory for it, and it takes that space until it is reclaimed by the garbage collector (AKA GC). Once it’s reclaimed, that memory can be reused by new objects.

It’s worth mentioning that the Heap contains all objects created in the application, regardless of the thread that created them. That is opposed to variables in the Thread Stack, which are only accessible within the context of the same thread, and are not visible to other threads.

### Heap Memory Structure

The heap itself can be divided into 2 main parts:

- Young Generation (AKA Nursery)— itself divided into 3 parts — Eden memory and two Survivor Memory spaces.
- Old (AKA Tenured) Generation

The Young Generation contains new objects that were recently allocated.  
These new objects begin their life in the Eden space. Once the Eden space is starting to fill up, Minor GC occurs (GC that happens relatively faster), and surviving objects move to the Survivor spaces. Once the Survivor space fills up, it also undergoes Minor GC, and if objects survive enough cycles of Minor GC, they are moved to the Old Generation.

The Old Generation contains objects that survived many cycles of Minor GC, and have proved themselves worthy. However, it too can fill up, initiating a Major GC, which usually takes longer.

The logic behind this separation is that most newly created objects are likely to be short-lived, so having this separation allows us to perform more efficient GC. More efficient GC allows us to free up memory at a faster rate when compared to performing GC on a single generational heap, and that means better performance overall.

It’s worth mentioning that there used to be another important area, separate from the main heap, called the Permanent Generation (AKA Perm/Perm Gen). It used to contain the application metadata, Java standard library classes, the String Pool, and more. Since Java 8 it does not exist, its content has been split between the main heap (String Pool) and the metaspace (application metadata).

## JVM Non-Heap Memory

In a nutshell, Non-Heap memory is everything that isn’t part of the Heap space. Simple right? well, you wouldn’t need an article for that if it was _that_ simple.

## Non-Heap Memory Structure

The Non-Heap memory (AKA Native Memory/Off-heap memory) is comprised of several areas:

- Metaspace
- Thread Memory (Stack Memory)
- Code Cache
- Garbage Collector Data

## The Metaspace

The [PermGen’s](https://www.javatpoint.com/permgen-space-java) successor. Introduced in Java 8.  
The Metaspace takes up a significant portion of the JVM’s Non-Heap memory. Its main purpose is to store class metadata — the internal runtime representation of Java classes.

By default, the metaspace is comprised of two parts — the Class Space and the Non-Class Space.

### The Class Space

Also called the Compressed Class Space, due to the use of [compressed class pointers](https://stuefe.de/posts/metaspace/what-is-compressed-class-space/). This space contains class-related metadata:

- [The “Klass” structure](https://openjdk.java.net/groups/hotspot/docs/StorageManagement.html) — contains the internal runtime representation of a class
- [The Vtable](https://wiki.openjdk.java.net/display/HotSpot/VirtualCalls) — A table that contains metadata for each virtual method the class has. (In Java, all overridable methods are virtual).
- The `Itable` — Similar to the Vtable, except it refers to inherited interface methods.
- The Oopmap —A structure that stores the positions of the class’s object- referencing data members. (In order to update their memory references upon Heap GC).

### The Non-Class Space

This space contains everything else:

- [The Constant Pool](https://www.baeldung.com/jvm-constant-pool) — a construct similar to a symbol table. Contains constants that enable the JVM to run a class’s code.
- Method metadata — the internal representation of methods in a class — the runtime equivalent of the [method_info](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.6) structure. Includes the method bytecode, exception table, etc.
- Method Counters — used for JIT compiler optimizations
- Annotation metadata
- and more.

It’s worth mentioning that in case the aforementioned [compressed class pointers](https://stuefe.de/posts/metaspace/what-is-compressed-class-space/) are disabled, all memory will be stored in one global metaspace context, and there will be no compressed class space.

### Memory Management

Data allocation in the Metaspace is tightly coupled to class loaders.  
When a class loader loads a class, it allocates memory in the metaspace for its metadata. That memory is owned by the class loader and is only released when the class loader itself is unloaded.

The Metaspace’s memory is controlled by three flags:

- `-XX:MaxMetaspaceSize` _—_ Controls the maximum memory that can be committed — both for class and non-class spaces. This size by default is practically unbounded (max 64-bit integer). The Metaspace grows dynamically by default, but will not exceed the aforementioned limit.
- `-XX:MetaspaceSize` _—_ Controls the memory threshold for GC. When this threshold is exceeded, GC will be triggered. To avoid frequent GC cycles, it is advised to go higher than the default size (12–20 MB depending on the platform), which is often too small for large-scale applications.
- `-XX:CompressedClassSpaceSize` _—_ Controls the memory reserved for the class space. As I’ve mentioned above, if compressed class pointers are disabled, there is no separation between class & non-class spaces, so this limit will be ignored.

### Metaspace vs. PermGen

There are a few key differences between the Metaspace and PermGen:

- It’s located outside the Heap as part of the native memory provided by the OS, whereas Permgen existed inside the Heap (although separated from the main heap)
- It Resizes dynamically at run-time — whereas Permgen had a fixed max size (although both have size-related configuration flags). It came as a much-needed remedy to the frequent “java.lang.OutOfMemoryError: PermGen” issue caused by Permgen’s small default size that didn’t scale well.
- It has more efficient GC — gets triggered automatically when class metadata usage reaches its maximum size, whereas Permgen had a fixed configured size which triggered frequent costly GC operations.

## Thread Memory — AKA Stack Memory

Each thread operating in the context of the JVM has its own memory — its own Stack. As its name suggests, the memory is organized as a LIFO data structure, which means new pieces of data are added at the top and are accessed first.

The stack contains every method the thread called that did not finish its execution, including every local variable inside of these methods.  
If the local variable is a primitive — it’s stored entirely in the stack (as such they are invisible to other threads). If it’s an object — only its reference is stored, and its actual value is stored on the Heap.

One of the more familiar issues with Stack memory is the StackOverflow error, which happens when the call stack is using more memory than was allocated to it. It’s usually caused by too many/infinite recursive calls.

### Main points about Thread/Stack memory:

- Memory allocation and de-allocation happen automatically once the method finished its execution.
- Memory handling is faster when compared to Heap memory.
- Memory allocated inside the stack can only be accessed within the thread’s context, making it safer — no other threads can access it. Whereas Heap memory is global and can potentially be accessed by any thread.
- Memory capacity tends to be significantly smaller compared to Heap memory.

## Code Cache

JVM-based applications are executed using a three-step process.

1. They are compiled from their respective languages (Java, Kotlin, Groovy, etc) into bytecode.
2. The JVM Interpreter then takes the compiled bytecode and interprets it into machine code.
3. The [JIT compiler](https://en.wikipedia.org/wiki/Just-in-time_compilation) performs additional optimizations during runtime, in order to gain a performance boost.

All of this optimized machine code needs to be stored somewhere. That’s where the Code Cache comes into play. The JVM uses the Code Cache to store all of the compiled bytecode it requires for the program’s execution.

Since Java 9, the Code Cache is segmented into three parts, in order to improve performance. (By improving Locality of Reference — similar pieces of code are stored in nearby memory locations for better access).  
Each segment contains compiled code of a particular type:

- Internal JVM code (compiler buffers, bytecode interpreter) — tends to stay in the Code Cache for the entire application’s lifespan. Its size is controlled by the `-XX:NonMethodCodeHeapSize` flag, and it tends to be fixed at around 3–5 MB.
- Lightly profiled code — Contains lightly optimized methods that have gone through basic profiling (courtesy of the JIT C1 compiler). Methods here tend to have a shorter lifespan. Its size is controlled by the  
    `-XX:ProfiledCodeHeapSize` flag.
- Non-profiled code — Contains Fully optimized code (courtesy of the JIT C2 compiler). Since methods here went through intensive optimization, they tend to have longer lifespans. This area’s size is controlled by the  
    `-XX:NonProfiledCodeHeapSize` flag.

### A few Gotcha’s

- The Code Cache has a fixed size. That means that when it gets full, the JIT compiler will effectively stop optimizing the code, which means our application’s performance will suffer. Luckily for us, we have a few useful flags we can use to mitigate this risk. In particular _ReservedCodeCacheSize_ for increasing the max size (default is 48MB, which is often too small), and `UseCodeCacheFlushing` to flush the Code Cache when certain conditions are met)
- When employing [tiered compilation](https://www.baeldung.com/jvm-tiered-compilation) (available since Java 7), the Code Cache becomes even more important, as the amount of compiled code increases significantly (2–4x). However, this increase naturally affects the size of the Code Cache itself, and if left unchecked, it could fill up and actually reduce the application’s performance.

## Garbage Collector Data

It may not come as a complete surprise, but in order to do its work, the Garbage Collector itself needs to keep data in memory.

Since its main job is to clean up the Heap, it makes sense that its own data must be stored outside the Heap, thus it is also part of the Non-Heap area.

The garbage collector can use many different algorithms to do its job. Each one of those algorithms uses data structures, and sometimes even multiple threads to do its work. This all takes up memory in the Non-heap area of the JVM. However, it’s worth mentioning that the amount of memory used is less dramatic than is warranted by the Starwars meme above, so it’s usually not a cause for concern.

## What we’ve learned today

It’s been quite a journey this time around. We discussed the Heap, its parts, and the way it operates. We also discussed the Non-Heap area, which is comprised of quite a few interesting areas, most notably the Metaspace.  
We even discussed the key flags that enable us to tune the various memory areas for better performance.

So the next time you find yourself looking at your application’s metrics in Grafana (or your favorite application monitoring dashboard), remember this article. It might just help you find a production bug, or even improve your application’s performance!



# 번역문

JVM은 모든 컴퓨터 과학 학부생이 마음 속으로 알고 있는 사랑받는 개념입니다. 1994년부터 우리와 함께 해왔고, 그 이후로도 꽤 많은 반복을 거쳤지만 여전히 견고하게 유지되고 있습니다.

# JVM 메모리 모델에 관심을 가져야 하는 이유는 무엇입니까?

그래프 데이터베이스에 Java, Kotlin, Scala, Groovy 또는 Neo4j를 사용하더라도 JVM은 뒤에서 작동합니다.

따라서 틈새 시장에서 더 강력한 명령을 내리려면 OOM 오류를 좋아하지 않는 한 그것이 어떻게 작동하는지, 특히 메모리 모델이 어떻게 구축되는지를 어느 정도 이해하는 것이 좋습니다.

우리 중 많은 사람들이 메모리 용량이 제한된 컨테이너 내에서 실행되는 마이크로서비스를 사용하고 있기 때문에 메모리 관리 방법을 이해하는 것은 오늘날 특히 중요합니다. 비용을 절감하고 애플리케이션 성능을 유지하려면 컨테이너의 메모리를 효율적으로 관리해야 합니다.

이제 질문하실 수 있습니다. 저는 DevOps 엔지니어가 아닙니다. 이것이 나와 어떤 관련이 있나요?  
우선, 저는 개인적으로 다음과 같은 실무 지식을 갖고 있다고 믿습니다. 전문 지식 범위를 벗어나더라도 기본 개념은 항상 좋은 생각입니다. 이를 통해 더욱 유용하고 다재다능해질 수 있으며 때로는 별개로 보이는 분야 사이의 점을 연결하여 창의적인 아이디어를 생각해낼 수도 있습니다. 둘째, 애플리케이션의 내부 작동 방식을 이해하고 잠재적인 문제나 잠재적인 개선 영역을 발견할 수 있으므로 애플리케이션을 더 잘 관리할 수 있습니다.

그럼 우리는 무엇을 기다리고 있나요? 자세히 살펴보겠습니다!  
JVM 메모리와 관련하여 중요한 차이점 중 하나는 애플리케이션의 다양한 측면에 영향을 미치기 때문에 메모리가 저장되는 위치입니다.

JVM 메모리를 대략적으로 두 부분으로 나눌 수 있습니다.

- 힙 메모리
- 비힙 메모리

대부분의 사람들에게 이미 친숙한 것, 즉 힙 메모리부터 시작해 보겠습니다.

# JVM 힙 메모리

대체로 JVM 힙은 객체(및 배열)로 구성됩니다.  
JVM이 시작되면 초기 크기와 증가할 수 있는 최대 크기로 힙이 생성됩니다. 예:

-Xms256m // 초기 힙 크기 256MB  
-Xmx2g // 최대 힙 크기 2GB 

새 개체를 생성할 때마다 JVM은 해당 개체에 메모리를 할당하고 가비지 수집기(GC)에서 회수할 때까지 해당 공간을 차지합니다. 일단 회수되면 해당 메모리는 새로운 객체에서 재사용될 수 있습니다.

힙에는 해당 개체를 생성한 스레드에 관계없이 애플리케이션에서 생성된 모든 개체가 포함되어 있다는 점을 언급할 가치가 있습니다. 이는 동일한 스레드의 컨텍스트 내에서만 액세스할 수 있고 다른 스레드에는 표시되지 않는 스레드 스택의 변수와 반대입니다.

## 힙 메모리 구조

힙 자체는 두 가지 주요 부분으로 나눌 수 있습니다.

- Young Generation(=Nursery) 자체는 3개의 부분, 즉 Eden 메모리와 두 개의 Survivor 메모리 공간으로 나뉩니다.
- Old(=Tenured) Generation

젊은 세대에는 최근에 할당된 새로운 개체가 포함되어 있습니다.  
이러한 새 개체는 Eden 공간에서 생명을 시작합니다. Eden 공간이 채워지기 시작하면 Minor GC(비교적 빠르게 발생하는 GC)가 발생하고 살아남은 객체는 Survivor 공간으로 이동합니다. Survivor 공간이 가득 차면 Minor GC도 거치게 되며, Minor GC의 주기가 충분히 지속되면 개체는 Old Generation으로 이동됩니다.

Old Generation에는 Minor GC의 여러 주기에서 살아남고 그 가치가 입증된 개체가 포함되어 있습니다. 그러나 이 역시 가득 차서 일반적으로 시간이 더 오래 걸리는 Major GC를 시작할 수 있습니다.

이러한 분리 뒤에 숨어 있는 논리는 새로 생성된 대부분의 개체가 수명이 짧을 가능성이 높기 때문에 이러한 분리를 통해 보다 효율적인 GC를 수행할 수 있다는 것입니다. 보다 효율적인 GC를 사용하면 단일 세대 힙에서 GC를 수행하는 것과 비교할 때 더 빠른 속도로 메모리를 확보할 수 있으며 이는 전체적으로 더 나은 성능을 의미합니다.

Permanent Generation(Perm/Perm Gen)이라고 하는 메인 힙과 별도로 또 다른 중요한 영역이 있었다는 점을 언급할 가치가 있습니다. 여기에는 애플리케이션 메타데이터, Java 표준 라이브러리 클래스, 문자열 풀 등이 포함되어 있었습니다. Java 8은 존재하지 않기 때문에 해당 내용이 main heap(String Pool)과 Metaspace(애플리케이션 메타데이터) 간에 분할되었습니다.

# JVM 비힙 메모리

간단히 말하면 비힙 메모리는 힙 공간의 일부가 아닌 모든 것입니다. 간단하죠? 글쎄요, 그것이 _그렇게_ 간단하다면 기사가 필요하지 않을 것입니다.

# 힙이 아닌 메모리 구조

비힙 메모리([[Native Memory(Java)|native memory]]/오프힙 메모리)는 여러 영역으로 구성됩니다.

- [[Metaspace]]
- 스레드 메모리([[Stack Area(Java)|stack]] 메모리)
- Code Cache
- [[Garbage Collection(java)|가비지 컬렉터]] 데이터

# 메타스페이스

[PermGen의](https://www.javatpoint.com/permgen-space-java) 후속 제품입니다. Java 8에서 도입되었습니다.  
메타공간은 JVM의 비힙 메모리의 상당 부분을 차지합니다. 주요 목적은 Java 클래스의 내부 런타임 표현인 클래스 메타데이터를 저장하는 것입니다.

기본적으로 메타공간은 클래스 공간과 비클래스 공간의 두 부분으로 구성됩니다.

## The Class Space

[압축된 클래스 포인터](https://stuefe.de/posts/metaspace/what-is-compressed-class-space/) 사용으로 인해 압축 클래스 공간(compressed class space)이라고도 합니다. 이 공간에는 class 관련 메타데이터가 포함되어 있습니다.

- [“Klass” 구조](https://openjdk.java.net/groups/hotspot/docs/StorageManagement.html) — 클래스의 내부 런타임 표현을 포함합니다
- [Vtable](https://wiki.openjdk.java.net/display/HotSpot/VirtualCalls) — 클래스에 있는 각 가상 메서드에 대한 메타데이터가 포함된 테이블입니다. (Java에서는 재정의 가능한 모든 메서드가 가상입니다.)
- The `Itable` — 상속된 인터페이스 메서드를 참조한다는 점을 제외하면 Vtable과 유사합니다.
- Oopmap - 클래스의 객체 참조 데이터 멤버 위치를 저장하는 구조입니다. (힙 GC 시 메모리 참조를 업데이트하기 위해)

## the Non-Class Space

이 공간에는 다음과 같은 모든 것이 포함되어 있습니다.

- [상수 풀](https://www.baeldung.com/jvm-constant-pool) — 기호 테이블과 유사한 구성입니다. JVM이 클래스 코드를 실행할 수 있도록 하는 상수가 포함되어 있습니다.
- 메서드 메타데이터 — 클래스에 있는 메서드의 내부 표현 — [method_info](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.6) 구조와 동일한 런타임입니다. 메소드 바이트코드, 예외 테이블 등을 포함합니다.
- 메소드 카운터(Method Counters) — JIT 컴파일러 최적화에 사용됩니다.
- Annotation metadata
- 그리고 더.

앞서 언급한 [압축 클래스 포인터](https://stuefe.de/posts/metaspace/what-is-compressed-class-space/)가 비활성화된 경우 모든 메모리가 하나의 전역 메타스페이스 컨텍스트에 저장된다는 점을 언급할 가치가 있습니다. 압축된 수업 공간이 없습니다.

## 메모리 관리

메타공간의 데이터 할당은 클래스 로더와 긴밀하게 연결됩니다.  
클래스 로더는 클래스를 로드할 때 해당 메타데이터를 위해 메타공간에 메모리를 할당합니다. 해당 메모리는 클래스 로더가 소유하며 클래스 로더 자체가 언로드될 때만 해제됩니다.

Metaspace의 메모리는 세 가지 플래그로 제어됩니다.

- `-XX:MaxMetaspaceSize` _—_ 클래스 공간과 비클래스 공간 모두에 대해 커밋할 수 있는 최대 메모리를 제어합니다. 기본적으로 이 크기는 실제로 제한이 없습니다(최대 64비트 정수). 메타스페이스는 기본적으로 동적으로 커지지만 앞서 언급한 한도를 초과하지 않습니다.
- `-XX:MetaspaceSize` _—_ GC의 메모리 임계값을 제어합니다. 이 임계값을 초과하면 GC가 트리거됩니다. 잦은 GC 주기를 방지하려면 기본 크기(플랫폼에 따라 12~20MB)보다 큰 값을 사용하는 것이 좋습니다. 이는 대규모 애플리케이션에 비해 너무 작은 경우가 많습니다.
- `-XX:CompressedClassSpaceSize` _—_ 수업 공간용으로 예약된 메모리를 제어합니다. 위에서 언급했듯이 압축 클래스 포인터가 비활성화되면 클래스와 클래스 포인터가 구분되지 않습니다. 수업 공간이 아니므로 이 제한은 무시됩니다.

## 메타스페이스 대 PermGen

Metaspace와 PermGen에는 몇 가지 주요 차이점이 있습니다.

- Permgen은 OS에서 제공하는 기본 메모리의 일부로 힙 외부에 위치하는 반면 Permgen은 힙 내부에 존재합니다(메인 힙과 분리되어 있음).
- 런타임 시 동적으로 크기가 조정됩니다. 반면 Permgen에는 고정된 최대 크기가 있었습니다(두 가지 모두 크기 관련 구성 플래그가 있음). 이는 확장이 잘 되지 않는 Permgen의 작은 기본 크기로 인해 자주 발생하는 "java.lang.OutOfMemoryError: PermGen" 문제에 대한 꼭 필요한 해결 방법이었습니다.
- 더 효율적인 GC가 있습니다. 클래스 메타데이터 사용량이 최대 크기에 도달하면 자동으로 트리거되는 반면 Permgen은 비용이 많이 드는 빈번한 GC 작업을 트리거하는 고정된 구성 크기를 가졌습니다.

# 스레드 메모리 — 일명 스택 메모리

JVM의 컨텍스트에서 작동하는 각 스레드에는 자체 메모리, 즉 자체 스택이 있습니다. 이름에서 알 수 있듯이 메모리는 LIFO 데이터 구조로 구성됩니다. 즉, 새로운 데이터 조각이 맨 위에 추가되고 먼저 액세스됩니다.

스택에는 스레드가 호출했지만 실행을 완료하지 못한 모든 메서드가 포함되어 있으며, 이러한 메서드 내부의 모든 지역 변수도 포함됩니다.  
로컬 변수가  primitive인 경우 - 스택에 완전히 저장됩니다(따라서 다른 스레드에는 보이지 않습니다). 객체인 경우 해당 참조만 저장되고 실제 값은 힙에 저장됩니다.

스택 메모리와 관련된 가장 친숙한 문제 중 하나는 호출 스택이 할당된 것보다 더 많은 메모리를 사용할 때 발생하는 StackOverflow 오류입니다. 일반적으로 너무 많거나 무한한 재귀 호출로 인해 발생합니다.

## 스레드/스택 메모리에 대한 주요 사항:

- 메서드 실행이 완료되면 메모리 할당 및 할당 취소가 자동으로 수행됩니다.
- 힙 메모리에 비해 메모리 처리 속도가 더 빠릅니다.
- 스택 내부에 할당된 메모리는 스레드의 컨텍스트 내에서만 액세스할 수 있으므로 더 안전합니다. 즉, 다른 스레드가 액세스할 수 없습니다. 반면 힙 메모리는 전역적이며 잠재적으로 모든 스레드에서 액세스할 수 있습니다.
- 메모리 용량은 힙 메모리에 비해 상당히 작은 경향이 있습니다.

# 코드 캐시

JVM 기반 애플리케이션은 3단계 프로세스를 통해 실행됩니다.

1. 해당 언어(Java, Kotlin, Groovy 등)에서 바이트코드로 컴파일됩니다.
2. 그런 다음 JVM 인터프리터는 컴파일된 바이트코드를 가져와 기계어 코드로 해석합니다.
3. [JIT 컴파일러](https://en.wikipedia.org/wiki/Just-in-time_compilation)는 성능 향상을 위해 런타임 중에 추가 최적화를 수행합니다.

이 최적화된 기계어 코드는 모두 어딘가에 저장되어야 합니다. 이것이 코드 캐시가 작동하는 곳입니다. JVM은 코드 캐시를 사용하여 프로그램 실행에 필요한 모든 컴파일된 바이트코드를 저장합니다.

Java 9부터 성능 향상을 위해 코드 캐시가 세 부분으로 분할되었습니다. (참조 집약성을 개선하여 유사한 코드 조각이 더 나은 액세스를 위해 가까운 메모리 위치에 저장됩니다.)  
각 세그먼트에는 특정 유형의 컴파일된 코드가 포함됩니다.

- 내부 JVM 코드(컴파일러 버퍼, 바이트코드 인터프리터) — 전체 애플리케이션 수명 동안 코드 캐시에 유지되는 경향이 있습니다. 크기는 `-XX:NonMethodCodeHeapSize` 플래그로 제어되며 약 3~5MB로 고정되는 경향이 있습니다.
- 간단하게 프로파일링된 코드 — 기본 프로파일링을 거친 가볍게 최적화된 메서드를 포함합니다(JIT C1 컴파일러 제공). 여기서 방법은 수명이 더 짧은 경향이 있습니다. 크기는  
    `-XX:ProfiledCodeHeapSize` 플래그로 제어됩니다.
- 프로파일링되지 않은 코드 — 완전히 최적화된 코드를 포함합니다(JIT C2 컴파일러 제공). 여기의 방법은 집중적인 최적화를 거쳤기 때문에 수명이 더 길어지는 경향이 있습니다. 이 영역의 크기는  
    `-XX:NonProfiledCodeHeapSize` 플래그에 의해 제어됩니다.

## 몇 가지 알아냈어요

- 코드 캐시의 크기는 고정되어 있습니다. 즉, 공간이 가득 차면 JIT 컴파일러가 코드 최적화를 효과적으로 중지하므로 애플리케이션 성능이 저하됩니다. 다행히도 이러한 위험을 완화하는 데 사용할 수 있는 몇 가지 유용한 플래그가 있습니다. 특히 _ReservedCodeCacheSize_ 최대 크기를 늘리고(기본값은 48MB로 너무 작은 경우가 많음)플러싱 특정 조건이 충족되면 코드 캐시) `UseCodeCacheFlushing`
- [계층형 컴파일](https://www.baeldung.com/jvm-tiered-compilation)(Java 7부터 사용 가능)을 사용하면 컴파일된 코드의 양이 크게 증가하므로 코드 캐시가 더욱 중요해집니다. (2~4배). 그러나 이러한 증가는 자연스럽게 코드 캐시 자체의 크기에 영향을 미치며, 확인하지 않은 채로 두면 가득 차서 실제로 애플리케이션 성능이 저하될 수 있습니다.

# 가비지 컬렉터 데이터

완전히 놀라운 일이 아닐 수도 있지만 작업을 수행하려면 가비지 컬렉터 자체가 데이터를 메모리에 보관해야 합니다.

주요 작업은 힙을 정리하는 것이므로 자체 데이터를 힙 외부에 저장해야 하므로 이 영역도 힙이 아닌 영역의 일부입니다.

가비지 수집기는 작업을 수행하기 위해 다양한 알고리즘을 사용할 수 있습니다. 각 알고리즘은 데이터 구조를 사용하며 때로는 여러 스레드를 사용하여 작업을 수행합니다. 이 모든 것은 JVM의 힙이 아닌 영역에서 메모리를 차지합니다. 그러나 사용된 메모리 양은 위의 Starwars 밈에서 보증한 것보다 덜 극적이므로 일반적으로 걱정할 필요가 없습니다.

# 오늘 우리가 배운 것

이번에는 정말 힘든 여정이었습니다. 우리는 힙과 그 부분, 그리고 작동 방식에 대해 논의했습니다. 우리는 또한 몇 가지 흥미로운 영역, 특히 메타스페이스로 구성된 비힙 영역에 대해서도 논의했습니다.  
더 나은 결과를 위해 다양한 메모리 영역을 조정할 수 있는 주요 플래그에 대해서도 논의했습니다. 성능.

따라서 다음에 Grafana(또는 자주 사용하는 애플리케이션 모니터링 대시보드)에서 애플리케이션의 측정항목을 볼 때 이 기사를 기억하세요. 이는 프로덕션 버그를 찾는 데 도움이 될 수도 있고 애플리케이션 성능을 향상시키는 데도 도움이 될 수 있습니다!