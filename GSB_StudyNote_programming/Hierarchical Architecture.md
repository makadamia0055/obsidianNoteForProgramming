---
tags:
  - hierarchical
  - architecture
aliases:
  - 계층형 아키텍처
  - Hierarchical Architecture
---
## Hierarchical Architecture VS [[layered architecture]]?
### GPT 3.5
"Hierarchical Architecture"와 "Layered Architecture"는 소프트웨어 아키텍처를 설계하는 두 가지 다른 개념입니다. 이 두 개념은 비슷한 목표를 가지고 있지만, 구현 방법에서 차이가 있습니다.

1. **Hierarchical Architecture (계층적 아키텍처):**
   - "Hierarchical"은 계층 구조를 의미하며, 이 아키텍처는 다양한 계층으로 구성됩니다.
   - 각 계층은 특정한 역할이나 책임을 갖고 있으며, 일반적으로 상위 계층은 하위 계층에 비해 더 추상화된 서비스를 제공합니다.
   - 상위 계층은 보통 더 일반적이고 범용적인 기능을 다루며, 하위 계층은 구체적이고 세부적인 기능을 다룹니다.
   - Hierarchical Architecture는 코드의 재사용성, 유지보수성, 시스템의 확장성을 향상시키기 위해 사용될 수 있습니다.

2. **Layered Architecture (계층형 아키텍처):**
   - "Layered"는 계층화된 구조를 의미하며, 이 아키텍처는 주로 수평적인 계층으로 구성됩니다.
   - 각 계층은 특정한 역할이나 책임을 가지며, 상위 계층은 일반적으로 하위 계층의 서비스를 이용합니다.
   - Layered Architecture에서는 주로 데이터 흐름이 상위 계층에서 하위 계층으로 향합니다.
   - 일반적으로 사용자 인터페이스(UI), 비즈니스 로직, 데이터 액세스와 같은 계층으로 나누어져 있습니다.

**관계:**
- "Hierarchical Architecture"는 전체 시스템을 계층적으로 구성하는 데 중점을 둔 일반적인 개념입니다.
- "Layered Architecture"는 계층적으로 구성된 아키텍처 중에서도 특히 수평적인 계층을 강조하는 하위 개념입니다.
- 즉, "Layered Architecture"는 "Hierarchical Architecture"의 한 형태로 볼 수 있습니다.
- 계층적인 구조를 통해 시스템을 모듈화하고 유지보수성을 높이는 것이 공통된 목표이지만, 구체적인 구현과 강조하는 측면에서 차이가 있습니다.

### Bing ai 대답

[Hierarchical Architecture와 Layered Architecture는 모두 소프트웨어 및 네트워크 설계에 사용되는 구조적 패턴입니다](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[1](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[2](https://www.auvik.com/franklyit/blog/hierarchical-network-design/). [두 아키텍처 모두 시스템을 여러 계층으로 분리하여 복잡성을 관리하고, 각 계층이 특정 기능을 수행하도록 합니다](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[1](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[2](https://www.auvik.com/franklyit/blog/hierarchical-network-design/)[3](https://www.geeksforgeeks.org/layered-architecture-in-computer-networks/).

[**Hierarchical Architecture**는 계층적 네트워크 디자인에서 주로 사용되며, 각 계층은 네트워크에서 특정 역할을 정의하는 일련의 기능을 가집니다](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[1](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[2](https://www.auvik.com/franklyit/blog/hierarchical-network-design/). [이 구조는 신뢰성, 확장성, 비용 효율성을 향상시키는 데 도움이 됩니다](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[1](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4).

[반면에, **Layered Architecture**는 소프트웨어 개발에서 주로 사용되며, 각 계층은 독립적으로 작동하고, 하위 계층에서 상위 계층으로 서비스를 제공합니다](https://www.baeldung.com/cs/layered-architecture)[4](https://www.baeldung.com/cs/layered-architecture)[3](https://www.geeksforgeeks.org/layered-architecture-in-computer-networks/). [이 구조는 변경이 특정 계층에만 영향을 미치도록 하여 유지 관리와 확장성을 향상시킵니다](https://www.baeldung.com/cs/layered-architecture)[4](https://www.baeldung.com/cs/layered-architecture)[3](https://www.geeksforgeeks.org/layered-architecture-in-computer-networks/).

[따라서, Hierarchical Architecture와 Layered Architecture는 모두 시스템을 여러 계층으로 분리하는 점에서 유사하지만, 사용되는 컨텍스트와 각 계층이 수행하는 역할에 따라 차이가 있습니다](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[1](https://www.ciscopress.com/articles/article.asp?p=2202410&seqNum=4)[4](https://www.baeldung.com/cs/layered-architecture)[2](https://www.auvik.com/franklyit/blog/hierarchical-network-design/)[3](https://www.geeksforgeeks.org/layered-architecture-in-computer-networks/).
## 레퍼런스
- [계층형 아키텍쳐란? - 조졸두](https://jojoldu.tistory.com/603)
- GPT 3.5, Bing