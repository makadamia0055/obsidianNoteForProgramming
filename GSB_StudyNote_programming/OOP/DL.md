---
tags:
  - denpendency
aliases:
  - Depencendy Lookup
  - DL
---
# DL란?
- 객체가 필요로 하는 의존성을 직접 찾아서 가져오는 패턴입니다.
    - 주로 서비스 로케이터(Service Locator)나 컨텍스트를 통해 의존성을 얻어옵니다.
    - 의존성을 가져오는 책임은 객체 자체에 있으며, 이는 객체가 필요로 하는 의존성을 스스로 결정하고 가져오게 됩니다.

```

public class Car {     
	private Engine engine;      
	public void start() {         
	// 서비스 로케이터를 통해 Engine 의존성을 얻어옴         
		this.engine = ServiceLocator.getEngine();         
		engine.start();     
		} 
	}

```

## [[DI]]와의 차이

- DI는 객체 외부에서 객체에게 의존성을 주입해주는 패턴.

## 레퍼런스
- 