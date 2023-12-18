---
tags:
  - 번역
  - 요약
  - 웹문서
---
원문
[OS 탐구 : Java 네이티브 메모리](https://picnic.app/careers/quest-to-the-os-java-native-memory)

# 원문
As developers we want to spend most time on new features and improvements, providing value to our users or customers. However, all software inevitably contains bugs and other defects. A lot can be done to catch defects before software reaches production (something Picnic’s automated fulfillment center team has [previously written about](https://blog.picnic.nl/reinventing-the-qa-process-25854fee51f3)) but some defects are likely to go undetected until the software is in the hands of users.

One such case happened when Picnic’s Distribution Systems team started to get notified that one of its deployments, the Fulfillment Service, was regularly killed and restarted by our Kubernetes cluster. This particular deployment houses many of the planning algorithms employed by Picnic, such as its [truck scheduling algorithm](https://blog.picnic.nl/its-in-our-dna-solving-logistics-problems-using-genetic-algorithms-a3d59e31558c).

Luckily we have [a broad range of monitoring and observability tools](https://blog.picnic.nl/monitoring-and-observability-at-picnic-684cefd845c4) set up across Picnic. After a quick peek at our dashboards we noticed the offending deployment showed continuously growing memory usage, eventually hitting its 6GB limit causing it to be killed.

![](https://miro.medium.com/max/676/0*shJJ5LNp9eDcky6E)

_Memory consumption over time of a Fulfillment Service instance, until it gets killed at 6GB._

This scenario is indicative of a memory leak, when resources consuming system memory are allocated but never released. Developers dread such problems, because they’re often tricky to reproduce and resolve. Not easily scared, we launched an investigation into what we assumed to be a memory leak. But after digging into the source of the problem, it turns out this behavior was not a memory leak, or at least not in the conventional sense.

To understand this, we need to embark on a quest into the concept of Java native memory. In this case, we define native memory as an umbrella term for all the memory requested by the JVM from the OS. The JVM can request this memory in a number of ways. We’ll explore each way, reaching progressively lower levels until we get to the core of the Fulfillment Service’s memory problems.

# Level 1: Heap

The heap is a JVM construct for memory management. Because of the heap and garbage collection developers don’t have to perform memory allocations themselves. It is backed by physical memory requested from the OS by the JVM. The heap expands automatically based on the application’s needs. However, it is bounded by a minimum and maximum size, specified through the JVM’s `-Xms` and `-Xmx` options respectively.

In the case of the Fulfillment Service, `-Xmx` is set to 4GB, so the heap alone should never exceed our 6GB container limit. This already rules out that the heap is solely responsible. But more interestingly, our dashboard reports only about 2.5GB of heap usage while the Fulfillment Service’s container is already consuming 6GB.

![](https://miro.medium.com/max/429/0*f115pL_RsWcmbd76)

_The Fulfillment Service’s 2.5GB heap size is not even close to the 6GB used_

Having determined that Java is the only process running in our container we concluded there must be more to this problem and continued our quest.

# Level 2: Off-heap

Any memory the JVM uses besides the heap is considered off-heap. There are a few categories of native memory that the JVM can track by default, such as memory used by the garbage collector, thread stacks, loaded classes, and more. To enable JVM native memory tracking we simply pass it the `-XX:NativeMemoryTracking=detail` flag, after which running `jcmd 1 VM.native_memory` (where 1 is our Java process ID) reports all native memory allocations known by the JVM.

![](https://miro.medium.com/max/683/0*rfUnK-b-XmqXUKeo)

_A (truncated) jcmd native memory report._

Conveniently, our monitoring also tracks all off-heap memory known to the JVM.

![](https://miro.medium.com/max/413/0*zLji7chRUuWSHxY4)

_The Fulfillment Service’s off-heap memory adds less than 500MB; still doesn’t add up to 6GB._

However, combining the heap and off-heap memory usage is still far from the 6GB limit. It turns out that there are various cases in which the JVM can allocate native memory without tracking it, taking us to the next level of this quest.

# Level 3: Direct Buffers

One specific source of native memory allocations not tracked by JVM native memory tracking is direct byte buffers. Java NIO exposes the possibility to [allocate blocks of native memory](https://docs.oracle.com/javase/7/docs/api/java/nio/ByteBuffer.html#allocateDirect(int)) that can be used by applications for any purpose. The advantage of using direct buffers instead of, for example, byte arrays is that they can be used in lower level operations (like writing to a socket) directly without costly conversion.

Even though the JVM does not track its direct byte buffer memory usage by default, we can measure these buffers’ sizes using a trick involving the `DirectByteBuffer` class. This class stores the location and size of a single direct byte buffer, so if we sum the size field of all `DirectByteBuffer` instances we’ll end up with our answer. There are some technicalities though. Firstly, some `DirectByteBuffer` instances may point at the physical memory owned by other instances (called “viewed” buffers). Secondly, `DirectByteBuffer` instances whose backing memory is likely already deallocated may still be around (“phantomed” or “phantom-reachable” buffers).

Luckily, there are some tools around to deal with these problems. For instance, IBM’s Eclipse Memory Analyzer extensions have a feature to [calculate the size of non-viewed non-phantomed direct byte buffers](https://www.ibm.com/docs/en/support-assistant/5.0.0?topic=se-directbytebuffers).

But for us, the Picnic monitoring setup makes it easy to get this data from our production system without resorting to analyzing memory dumps.

![](https://miro.medium.com/max/413/0*ZlGt5IypIrbQjidT)

_The Fulfillment Service is using around 600MB memory for direct buffers_

Unfortunately, adding this to all the previous sources of native memory usage we still end up with under 4GB of memory out of the 6GB we need to account for. However, we haven’t yet exhausted the possible ways native memory can be allocated by the JVM. So again our quest carries on and we’re now tasked with tracking all native memory allocations.

# Level 4: Native Memory allocations through JNI

The final possibility of native memory allocation from the JVM comes from libraries backed by native code. In some cases it makes more sense to implement functionality as native code, generated by compilers for languages such as C, C++ or Rust. Native code can perform better, or it might be the only way to access certain OS level functionality. Java allows calling native code through the Java Native Interface (JNI). Code called through JNI manages its own memory, so this memory is not tracked by the JVM and not automatically cleaned up through garbage collection. In a language like C, memory is managed by calling the `malloc` and `free` functions, which respectively request memory from and return memory to the OS. This is far from the complete picture, but more on that in the next level.

These two functions, `malloc` and `free`, are implemented in their own library. The default on most flavors of Linux is GNU malloc, but it can be swapped out for other implementations. One such implementation is [jemalloc](https://github.com/jemalloc/jemalloc), which conveniently also allows tracking where `malloc` is being called from. This gives us the opportunity to see whether there are any native functions allocating increasing amounts of memory.

On Linux, jemalloc can be enabled by bundling its shared library with an application and setting the `LD_PRELOAD` environment variable to the location of `libjemalloc.so` before running Java. Memory profiling can be enabled through the `MALLOC_CONF` environment variable. The jemalloc wiki contains [some useful examples](https://github.com/jemalloc/jemalloc/wiki/Use-Case%3A-Heap-Profiling). You can check the [jemalloc man page](http://jemalloc.net/jemalloc.3.html) for a full reference of these options. Now our application writes `.heap` files after a set volume of allocations. These files can be parsed using the `jeprof` command into something human-readable.

![](https://miro.medium.com/max/493/0*3ttvaq3eCkpdPXnj)

_An extract from the_ `_jemalloc_` _profiling output. Here we see that_ `_Unsafe_allocateMemory0_` _calls_ `_malloc_`_. This function is responsible for allocating memory backing the direct byte buffers discussed above._

Jemalloc only samples memory allocations instead of measuring every single `malloc` call to prevent excessive resource consumption. Therefore, the output of `jeprof` cannot be directly interpreted as the number of bytes currently in use. However, it does allow us to spot any suspicious functions allocating native memory. Additionally, we could also spot functions that are holding on to significantly more memory relative to others (potentially indicating a memory leak).

In our case, we spotted neither. The only functions directly calling `malloc` were `Unsafe_allocateMemory0` and `AllocateHeap`, both of which are implemented in OpenJDK and were investigated through the other methods explained above. However, after we enabled jemalloc in the Fulfillment Service we observed something interesting: the memory had stopped growing and stabilized around 4GB.

![](https://miro.medium.com/max/574/0*djc1iQKK1zR5CA_H)

_The Fulfillment Service’s Kubernetes memory usage shows very different behavior when jemalloc is enabled._

Even though on the surface we had solved our problem, there must be a full explanation for this dramatic change. And our quest ensues, striving to unearth what was going on in the dangerous depths of our JVM’s native memory.

# Boss Fight: Malloc

If there wouldn’t be advantages to picking one malloc library over the other, there wouldn’t be any competing implementations. In fact there are a number of different malloc libraries, each having surprisingly different internals. As an example, let’s look at GNU malloc, the `malloc` implementation originally used by our Fulfillment Service.

Under the hood, GNU malloc uses various data structures to make memory allocations more efficient. One such data structure is a collection of heaps. When the application calls `malloc`, one of these heaps is searched for a contiguous free chunk of memory big enough to fit the request. When the application calls free, a chunk of the heap frees up, which can be reused by a future `malloc` call. An important detail is that only the topmost chunk of each heap is available for returning to the OS. All empty chunks in the middle of heaps will technically be unused, but still count towards the memory the application is using. This is a very simplified view of what’s happening; check the glibc wiki for a [complete overview of GNU malloc internals](https://sourceware.org/glibc/wiki/MallocInternals).

![](https://miro.medium.com/max/352/0*lmNF2Veec6mh8Y_r)

_Simplified representation of a GNU malloc heap. A malloc call requests memory. The request does not fit in the lowest free chunk, but does fit in the one above. The lower chunk will remain unused until a request less than or equal its size arrives._

As you might imagine, there could be many chunks in the malloc heaps just sitting around empty, depending on the pattern of `malloc` and `free` calls an application executes. At this point we were wondering if this kind of memory fragmentation could explain the Fulfillment Service’s ever growing memory usage. While investigating possible ways to confirm this, we happened upon [an excellent article from the LinkedIn engineering team](https://engineering.linkedin.com/blog/2021/taming-memory-fragmentation-in-venice-with-jemalloc), describing a problem extremely similar to ours. However, instead of using the [gdb-heap](https://github.com/rogerhu/gdb-heap) tool the LinkedIn team used, we decided to confirm our hypothesis in a slightly more direct way.

It turns out that GNU malloc already [exposes some statistics](http://www.gnu.org/software/libc/manual/html_node/Statistics-of-Malloc.html) which are suitable for roughly quantifying memory fragmentation. The statistics we are interested in are: the total size of memory in use by the application, the total size of memory in malloc’s heaps but not in use by the application and the part of that memory allowed to be returned to the OS. GNU malloc’s `mallinfo` function returns these as the `uordblks`, `fordblks` and `keepcost` fields respectively. So, calculating `1 — uordblks / (fordblks — keepcost)` gives the ratio of unused memory that cannot be returned to the OS, a measure of memory fragmentation.

![](https://miro.medium.com/proxy/0*OAeiO0X8-ob8E7Ao)

_Simplified representation of a GNU malloc heap with fields returned by mallinfo indicated._

Having learned this, we created a local testing setup of the Fulfillment Service. This setup consisted of a Docker container with gdb (the GNU debugger for debugging native code), OpenJDK with debug symbols and glibc with debug symbols. This setup allowed us to attach gdb to the JVM and call the `mallinfo` function from the gdb prompt.

To measure memory fragmentation we booted this container and locally ran a production-like workload, consisting of a number of steps in the distribution planning process. After running this planning, memory fragmentation was around 80%. This in itself does not say much since it’s likely that part of this memory can be reclaimed during future memory allocations. However, after another planning memory fragmentation was up to 84%, leading us to conclude that each planning leaves behind unused memory that won’t be used again.

The reason this phenomenon disappears when using jemalloc is that the algorithms and data structures in that malloc implementation are specifically designed to put an upper bound on memory fragmentation. Having confirmed that fragmentation was indeed the root cause of our memory issues we decided to enable jemalloc permanently. Since then memory usage has been very well-behaved and the Fulfillment Service has not been killed anymore.

# Conclusion

What seemed to be a memory leak eventually took us on a quest all the way down to the level of the OS. This goes to show that even though Java hides a lot of complexity surrounding memory management, sometimes we still need to break out of the comfort of the JVM’s heap and get our hands dirty. In the end, the solution to enable jemalloc was easy to implement. Concluding this was the right solution, however, was definitely non-trivial.

After our investigation it might seem tempting to switch to jemalloc for all workloads within Picnic, but things are not so simple. The Fulfillment Service is under heavy load during plannings while being almost idle the rest of the time. In this case switching to jemalloc did not significantly impact performance and gave us jemalloc’s promised upper bound on memory usage, keeping the service from being killed. However, services with a more evenly distributed load will have a different memory allocation pattern. Therefore, careful benchmarking will be necessary before switching to jemalloc.


# 번역
개발자로서 우리는 새로운 기능과 개선 사항에 대부분의 시간을 투자하여 사용자나 고객에게 가치를 제공하고자 합니다. 그러나 모든 소프트웨어에는 필연적으로 버그 및 기타 결함이 포함되어 있습니다. 소프트웨어가 프로덕션에 도달하기 전에 결함을 발견하기 위해 많은 조치를 취할 수 있지만(Picnic의 자동화된 주문 처리 센터 팀이 [이전에 작성한 내용](https://blog.picnic.nl/reinventing-the-qa-process-25854fee51f3) 내용) 일부 결함은 발견되지 않을 가능성이 높습니다. 소프트웨어가 사용자의 손에 들어갈 때까지

Picnic의 배포 시스템 팀이 배포 중 하나인 Fulfillment Service가 Kubernetes 클러스터에 의해 정기적으로 종료되고 다시 시작되었다는 알림을 받기 시작했을 때 그러한 사례가 발생했습니다. 이 특정 배포에는 [트럭 예약 알고리즘](https://blog.picnic.nl/its-in-our-dna-solving-logistics-problems-using-genetic-algorithms-a3d59e31558c)과 같이 Picnic에서 사용하는 많은 계획 알고리즘이 포함되어 있습니다.

다행히도 Picnic에는 [다양한 모니터링 및 관찰 도구](https://blog.picnic.nl/monitoring-and-observability-at-picnic-684cefd845c4) 가 설정되어 있습니다. 대시보드를 잠깐 살펴본 후 문제가 있는 배포에서 메모리 사용량이 지속적으로 증가하여 결국 6GB 제한에 도달하여 종료되는 것을 발견했습니다.

![](https://miro.medium.com/max/676/0*shJJ5LNp9eDcky6E)

_6GB에서 종료될 때까지 Fulfillment Service 인스턴스의 시간 경과에 따른 메모리 소비입니다._

이 시나리오는 시스템 메모리를 소비하는 리소스가 할당되었지만 해제되지 않은 경우 메모리 누수를 나타냅니다. 개발자는 이러한 문제를 재현하고 해결하기가 까다롭기 때문에 두려워합니다. 쉽게 두려워하지 않고 우리는 메모리 누수로 추정되는 것에 대한 조사를 시작했습니다. 그러나 문제의 원인을 파헤쳐 본 결과 이 ​​동작은 메모리 누수가 아니거나 적어도 일반적인 의미가 아니라는 것이 밝혀졌습니다.

이를 이해하려면 Java [[Native Memory(Java)|native memory]] 개념에 대한 탐구를 시작해야 합니다. 이 경우 OS에서 JVM이 요청한 모든 메모리에 대한 포괄적인 용어로 native memory를 정의합니다. JVM은 다양한 방법으로 이 메모리를 요청할 수 있습니다. 우리는 Fulfillment Service의 메모리 문제의 핵심에 도달할 때까지 점진적으로 낮은 수준에 도달하면서 각 방법을 탐색할 것입니다.

# 수준 1: [[Heap Area(Java)|Heap]]

힙은 메모리 관리를 위한 JVM 구성입니다. 힙 및 [[Garbage Collection(java)|Garbage Collection]]으로 인해 개발자는 메모리 할당을 직접 수행할 필요가 없습니다. 이는 JVM이 OS에서 요청한 물리적 메모리로 지원됩니다. 힙은 애플리케이션의 필요에 따라 자동으로 확장됩니다. 그러나 JVM의 `-Xms` 및 `-Xmx` 옵션을 통해 각각 지정된 최소 및 최대 크기로 제한됩니다.

처리 서비스의 경우 `-Xmx` 4GB로 설정되므로 힙 자체만 6GB 컨테이너 한도를 초과해서는 안 됩니다. 이는 이미 힙이 전적으로 책임이 있다는 점을 배제합니다. 그러나 더 흥미로운 점은 Fulfillment Service의 컨테이너가 이미 6GB를 소비하고 있는 반면 대시보드에서는 약 2.5GB의 힙 사용량만 보고한다는 것입니다.

![](https://miro.medium.com/max/429/0*f115pL_RsWcmbd76)

_Fulfillment Service의 2.5GB 힙 크기는 사용된 6GB에 가깝지도 않습니다._

Java가 컨테이너에서 실행되는 유일한 프로세스라고 판단한 후 우리는 이 문제에 더 많은 문제가 있을 것이라고 결론을 내리고 탐구를 계속했습니다.

# 수준 2: Off-heap

JVM이 힙 외에 사용하는 모든 메모리는 오프힙으로 간주됩니다. [[Garbage Collection(java)|GC]], 스레드 스택, 로드된 클래스 등에 사용되는 메모리와 같이 JVM이 기본적으로 추적할 수 있는 몇 가지 기본 메모리 범주가 있습니다. JVM 네이티브 메모리 추적을 활성화하려면 `-XX:NativeMemoryTracking=detail` 플래그를 전달하면 됩니다. 그 후 실행하면 `jcmd 1 VM.native_memory` (여기서 1은 Java 프로세스 ID)가 알려진 모든 네이티브 메모리 할당을 보고합니다. JVM.

![](https://miro.medium.com/max/683/0*rfUnK-b-XmqXUKeo)

_(turncated) jcmd native memory 보고서._

편리하게도 모니터링을 통해 JVM에 알려진 모든 Off-heap 메모리도 추적합니다.

![](https://miro.medium.com/max/413/0*zLji7chRUuWSHxY4)

_Fulfillment Service의 오프힙 메모리는 500MB 미만을 추가합니다. 여전히 최대 6GB까지 추가되지 않습니다._

그러나 힙과 오프힙 메모리 사용량을 합치는 것은 여전히 ​​6GB 제한과는 거리가 멀습니다. JVM이 기본 메모리를 추적하지 않고 할당할 수 있는 다양한 경우가 있다는 사실이 밝혀져 이 퀘스트의 다음 단계로 넘어갑니다.

# 레벨 3: [[Direct buffers]]

JVM 기본 메모리 추적으로 추적되지 않는 기본 메모리 할당의 특정 소스 중 하나는 직접 바이트 버퍼입니다. Java NIO는 애플리케이션에서 어떤 목적으로든 사용할 수 있는 [기본 메모리 블록을 할당](https://docs.oracle.com/javase/7/docs/api/java/nio/ByteBuffer.html#allocateDirect(int)) 할 수 있는 가능성을 제공합니다. 예를 들어 바이트 배열 대신 직접 버퍼를 사용하면 비용이 많이 드는 변환 없이 하위 수준 작업(소켓에 쓰기 등)에 직접 사용할 수 있다는 장점이 있습니다.

JVM이 기본적으로 직접 바이트 버퍼 메모리 사용량을 추적하지 않더라도 `DirectByteBuffer` 클래스와 관련된 트릭을 사용하여 이러한 버퍼의 크기를 측정할 수 있습니다. 이 클래스는 단일 직접 바이트 버퍼의 위치와 크기를 저장하므로 모든 `DirectByteBuffer` 인스턴스의 크기 필드를 합산하면 답이 나옵니다. 하지만 몇 가지 기술이 있습니다. 첫째, 일부 `DirectByteBuffer` 인스턴스는 다른 인스턴스가 소유한 물리적 메모리('조회된' 버퍼라고 함)를 가리킬 수 있습니다. 둘째, `DirectByteBuffer` 지원 메모리가 이미 할당 해제되었을 가능성이 있는 인스턴스가 여전히 주변에 있을 수 있습니다('유령' 또는 '유령 연결 가능' 버퍼).

다행히도 이러한 문제를 해결할 수 있는 몇 가지 도구가 있습니다. 예를 들어, IBM의 Eclipse 메모리 분석기 확장에는 [보이지 않는 팬텀 직접 바이트 버퍼의 크기를 계산](https://www.ibm.com/docs/en/support-assistant/5.0.0?topic=se-directbytebuffers)하는 기능이 있습니다.

하지만 우리의 경우 Picnic 모니터링 설정을 사용하면 메모리 덤프를 분석하지 않고도 생산 시스템에서 이 데이터를 쉽게 얻을 수 있습니다.

![](https://miro.medium.com/max/413/0*ZlGt5IypIrbQjidT)

_Fulfillment Service는 직접 버퍼에 약 600MB의 메모리를 사용하고 있습니다._

안타깝게도 이를 이전의 모든 기본 메모리 사용량 소스에 추가하면 우리가 고려해야 할 6GB 중 4GB 미만의 메모리만 남게 됩니다. 그러나 우리는 JVM이 네이티브 메모리를 할당할 수 있는 가능한 방법을 아직 다 사용하지 않았습니다. 다시 한번 우리의 탐구는 계속되고 이제 우리는 모든 기본 메모리 할당을 추적하는 임무를 맡게 되었습니다.

# 레벨 4: [[JNI]]를 통한 [[Native Memory(Java)|native memory]] 할당

JVM에서 네이티브 메모리 할당의 마지막 가능성은 네이티브 코드로 지원되는 라이브러리에서 비롯됩니다. 어떤 경우에는 C, C++ 또는 Rust와 같은 언어용 컴파일러에서 생성된 네이티브 코드로 기능을 구현하는 것이 더 합리적입니다. 네이티브 코드는 더 나은 성능을 발휘할 수 있거나 특정 OS 수준 기능에 액세스하는 유일한 방법일 수 있습니다. Java에서는 JNI(Java Native Interface)를 통해 네이티브 코드를 호출할 수 있습니다. JNI를 통해 호출된 코드는 자체 메모리를 관리하므로 이 메모리는 JVM에서 추적되지 않으며 가비지 수집을 통해 자동으로 정리되지 않습니다. C와 같은 언어에서 메모리는 각각 OS에 메모리를 요청하고 OS에 반환하는 `malloc` 및 `free` 함수를 호출하여 관리됩니다. 이는 완전한 그림과는 거리가 멀지만 다음 단계에서 이에 대해 자세히 설명합니다.

이 두 함수 `malloc` 및 `free`는 자체 라이브러리에서 구현됩니다. 대부분의 Linux 버전에서 기본값은 GNU malloc이지만 다른 구현으로 교체할 수 있습니다. 이러한 구현 중 하나는 [jemalloc](https://github.com/jemalloc/jemalloc)이며, 이를 통해 `malloc` 가 호출되는 위치를 편리하게 추적할 수도 있습니다. 이를 통해 점점 더 많은 양의 메모리를 할당하는 기본 함수가 있는지 확인할 수 있습니다.

Linux에서는 공유 라이브러리를 애플리케이션과 번들로 묶고 Java를 실행하기 전에 `LD_PRELOAD` 환경 변수를 `libjemalloc.so` 위치로 설정하여 jemalloc을 활성화할 수 있습니다. . 메모리 프로파일링은 `MALLOC_CONF` 환경 변수를 통해 활성화할 수 있습니다. jemalloc 위키에는 [몇 가지 유용한 예](https://github.com/jemalloc/jemalloc/wiki/Use-Case%3A-Heap-Profiling)가 포함되어 있습니다. 이러한 옵션에 대한 전체 참조는 [jemalloc 매뉴얼 페이지](http://jemalloc.net/jemalloc.3.html) 를 확인하세요. 이제 애플리케이션은 설정된 할당량 이후에 `.heap` 파일을 작성합니다. 이러한 파일은 `jeprof` 명령을 사용하여 사람이 읽을 수 있는 형태로 파싱할 수 있습니다.

![](https://miro.medium.com/max/493/0*3ttvaq3eCkpdPXnj)

_프로파일링 출력_ `_jemalloc_` _에서 발췌. 여기서는_ `_Unsafe_allocateMemory0_` _전화_ `_malloc_`_를 볼 수 있습니다. 이 함수는 위에서 설명한 직접 바이트 버퍼를 지원하는 메모리 할당을 담당합니다._

Jemalloc은 과도한 리소스 소비를 방지하기 위해 매 `malloc` 호출을 모두 측정하는 대신 메모리 할당만 샘플링합니다. 따라서 `jeprof` 의 출력은 현재 사용 중인 바이트 수로 직접 해석될 수 없습니다. 그러나 이를 통해 네이티브 메모리를 할당하는 의심스러운 기능을 발견할 수 있습니다. 또한 다른 함수에 비해 훨씬 더 많은 메모리를 보유하고 있는 함수를 발견할 수도 있습니다(잠재적으로 메모리 누수를 나타냄).

저희 경우에는 둘 다 발견하지 못했습니다. `malloc` 를 직접 호출하는 유일한 함수는 `Unsafe_allocateMemory0` 및 `AllocateHeap`였으며 둘 다 OpenJDK에서 구현되었으며 설명된 다른 방법을 통해 조사되었습니다. 위에. 그러나 Fulfillment Service에서 jemalloc을 활성화한 후 흥미로운 점을 발견했습니다. 메모리가 증가를 멈추고 4GB 정도에서 안정화되었습니다.

![](https://miro.medium.com/max/574/0*djc1iQKK1zR5CA_H)

_Fulfillment Service의 Kubernetes 메모리 사용량은 jemalloc이 활성화되면 매우 다른 동작을 보여줍니다._

표면적으로는 문제가 해결되었지만 이 극적인 변화에 대한 충분한 설명이 있어야 합니다. 그리고 JVM의 기본 메모리의 위험한 깊이에서 무슨 일이 일어나고 있는지 알아내기 위해 노력하는 우리의 탐구가 이어집니다.

# 보스전: Malloc

다른 것보다 하나의 malloc 라이브러리를 선택하는 데 이점이 없다면 경쟁 구현도 없을 것입니다. 실제로 다양한 malloc 라이브러리가 있으며, 각 라이브러리는 놀라울 정도로 다른 내부를 가지고 있습니다. 예를 들어, 원래 Fulfillment Service에서 사용했던 구현인 GNU malloc을 살펴보겠습니다. `malloc` 

내부적으로 GNU malloc은 다양한 데이터 구조를 사용하여 메모리 할당을 보다 효율적으로 만듭니다. 그러한 데이터 구조 중 하나는 힙 모음입니다. 애플리케이션이 `malloc`을 호출하면 이러한 힙 중 하나가 요청에 맞을 만큼 충분히 큰 연속된 여유 메모리 청크를 검색합니다. 애플리케이션이 무료를 호출하면 힙의 일부가 해제되어 향후 `malloc` 호출에서 재사용될 수 있습니다. 중요한 세부 사항은 각 힙의 최상위 청크만 OS로 돌아갈 수 있다는 것입니다. 힙 중간에 있는 모든 빈 청크는 기술적으로 사용되지 않지만 여전히 애플리케이션이 사용하는 메모리에 포함됩니다. 이것은 무슨 일이 일어나고 있는지에 대한 매우 단순화된 보기입니다. [GNU malloc 내부 전체 개요](https://sourceware.org/glibc/wiki/MallocInternals)는 glibc wiki를 확인하세요.

![](https://miro.medium.com/max/352/0*lmNF2Veec6mh8Y_r)

_GNU malloc 힙의 단순화된 표현입니다. malloc 호출은 메모리를 요청합니다. 요청은 가장 낮은 여유 청크에 맞지 않지만 위의 청크에는 맞습니다. 하위 청크는 해당 크기보다 작거나 같은 요청이 도착할 때까지 사용되지 않은 상태로 유지됩니다._

상상할 수 있듯이, `malloc` 및 `free` 호출 패턴에 따라 빈 자리에 앉아 있는 malloc 힙에 많은 청크가 있을 수 있습니다. 응용 프로그램이 실행됩니다. 이 시점에서 우리는 이러한 종류의 메모리 조각화가 Fulfillment Service의 계속 증가하는 메모리 사용량을 설명할 수 있는지 궁금했습니다. 이를 확인할 수 있는 가능한 방법을 조사하던 중 우리와 매우 유사한 문제를 설명하는 [LinkedIn 엔지니어링 팀의 훌륭한 기사](https://engineering.linkedin.com/blog/2021/taming-memory-fragmentation-in-venice-with-jemalloc)를 발견했습니다. 그러나 LinkedIn 팀이 사용한 [gdb-heap](https://github.com/rogerhu/gdb-heap) 도구 대신 우리는 좀 더 직접적인 방법으로 가설을 확인하기로 결정했습니다.

GNU malloc은 이미 [일부 통계를 노출](http://www.gnu.org/software/libc/manual/html_node/Statistics-of-Malloc.html) 하여 메모리 조각화를 대략적으로 정량화하는 데 적합한 것으로 나타났습니다. 우리가 관심을 갖는 통계는 애플리케이션에서 사용 중인 총 메모리 크기, 애플리케이션에서 사용하지 않는 malloc 힙의 총 메모리 크기, OS에 반환되도록 허용된 메모리 부분입니다. GNU malloc의 `mallinfo` 함수는 이를 각각 `uordblks`, `fordblks` 및 `keepcost` 필드로 반환합니다. 따라서 계산하면 `1 — uordblks / (fordblks — keepcost)` OS에 반환할 수 없는 사용되지 않은 메모리의 비율, 즉 메모리 조각화의 척도가 제공됩니다.

![](https://miro.medium.com/proxy/0*OAeiO0X8-ob8E7Ao)

_표시된 mallinfo에 의해 반환된 필드가 있는 GNU malloc 힙의 단순화된 표현입니다._

이 사실을 알게 된 후 Fulfillment Service의 로컬 테스트 설정을 만들었습니다. 이 설정은 gdb(네이티브 코드 디버깅을 위한 GNU 디버거)가 있는 Docker 컨테이너, 디버그 기호가 있는 OpenJDK, 디버그 기호가 있는 glibc로 구성되었습니다. 이 설정을 통해 우리는 gdb를 JVM에 연결하고 gdb 프롬프트에서 `mallinfo` 함수를 호출할 수 있었습니다.

메모리 조각화를 측정하기 위해 우리는 이 컨테이너를 부팅하고 배포 계획 프로세스의 여러 단계로 구성된 프로덕션과 유사한 워크로드를 로컬에서 실행했습니다. 이 계획을 실행한 후 메모리 조각화는 약 80%였습니다. 향후 메모리 할당 중에 이 메모리의 일부가 회수될 가능성이 높기 때문에 이것 자체로는 많은 것을 의미하지 않습니다. 그러나 또 다른 계획 이후에는 메모리 조각화가 최대 84%에 달해 각 계획마다 다시 사용되지 않는 미사용 메모리가 남는다는 결론을 내릴 수 있었습니다.

jemalloc을 사용할 때 이 현상이 사라지는 이유는 해당 malloc 구현의 알고리즘과 데이터 구조가 메모리 조각화의 상한선을 설정하도록 특별히 설계되었기 때문입니다. 조각화가 실제로 메모리 문제의 근본 원인이라는 것을 확인한 후 우리는 jemalloc을 영구적으로 활성화하기로 결정했습니다. 그 이후로 메모리 사용량은 매우 잘 작동했으며 Fulfillment Service는 더 이상 종료되지 않았습니다.

# 결론

메모리 누수로 보였던 문제는 결국 우리를 OS 수준까지 탐구하게 만들었습니다. 이는 Java가 메모리 관리와 관련된 많은 복잡성을 숨기더라도 때로는 여전히 JVM 힙의 편안함에서 벗어나 손을 더럽힐 필요가 있음을 보여줍니다. 결국 jemalloc을 활성화하는 솔루션은 구현하기 쉬웠습니다. 그러나 이것이 올바른 해결책이라고 결론을 내리는 것은 확실히 사소한 일이 아니었습니다.

조사 결과 Picnic 내의 모든 작업 부하에 대해 jemalloc으로 전환하고 싶은 유혹이 생길 수 있지만 상황은 그렇게 간단하지 않습니다. 이행 서비스는 계획 중에 과도한 부하를 받고 있으며 나머지 시간에는 거의 유휴 상태입니다. 이 경우 jemalloc으로 전환해도 성능에 큰 영향을 미치지 않았으며 jemalloc이 약속한 메모리 사용량 상한선을 제공하여 서비스가 중단되는 것을 방지했습니다. 그러나 로드가 보다 고르게 분산된 서비스는 메모리 할당 패턴이 다릅니다. 따라서 jemalloc으로 전환하기 전에 세심한 벤치마킹이 필요할 것입니다.