---
tags:
---
---
### R-Tree 알고리즘
- 2차원의 데이터를 인덱싱하고 검색하는 목적의 인덱스인 공간 인덱스(Spatial Index)에서 사용되는 [[트리 알고리즘(DB)|트리]] 알고리즘.
	- 기본적인 내부 메커니즘은 B-Tree와 흡사
		- B-Tree는 인덱스를 구성하는 칼럼의 값이 1차원의 [[물리량|스칼라]]인 반면, R-Tree인덱스는 2차원의 공간 개념 값을 가짐.
	- GPS, GIS에 기반을 둔 서비스에 사용되며, MySQL에서는 공간 확장(Spatial Extension)을 이용하면 간단하게 이러한 기능을 구현할 수 있다. 
	- MySQL 공간확장의 기능 세 가지
		- 공간 데이터를 저장할 수 있는 데이터 타입
		- 공간 데이터 검색을 위한 공간 인덱스(R-Tree 알고리즘)
		- 공간 데이터의 연산 함수(거리 또는 포함 관계의 처리)
	- (더 자세한 내용은 12장 2절 공간 검색

# 8.4.1 구조 및 특성
- MySQL은 공간 정보의 저장 및 검색을 위해 여러 가지 기하학적 도형(Geometry) 정보를 관리할 수 있는 데이터 타입을 제공.
- 대표적으로 MySQL에서 지원하는 데이터 타입
![[GEOMETRY 데이터 타입.png]]
- 마지막에 있는 GEOMETRY 타입은 나머지 세 타입에 대한 슈퍼 타입. 
	- POINT, LINE, POLYGON 객체를 모두 저장 가능.
### MBR(Minimum Bounding Rectangle, 최소 경계 상자)
- 해당 도형을 감싸는 최소 크기의 사각형. 
![[최소 경계 상자 MBR.png]]
- 이 사각형들의 포함 관계를 B-Tree로 구현한 인덱스가 바로 R-Tree 인덱스이다. 
![[공간(Spatial) 데이터.png]]
- 공간(Spatial) 데이터와 MBR 분류
	- 상기 도형들의 MBR을 아래 세 개의 레벨로 나눠서 그려본 그림은 다음과 같다. 
		1. 최상위 레벨 : R1, R2
		2. 차상위 레벨 : R3, R3, R5, R6
			-  차상위 레벨의 MBR은 중간 크기의 MBR이다. 
		3. 최하위 레벨 : R7 ~ R14
			- 최하위 레벨의 MBR은 각 도형 데이터의 MBR을 의미
![[공간(Spatial)데이터의 MBR.png]]
- 최상위 레벨의 MBR은 루트 노드에 저장되는 정보, 차상위 그룹 MBR은 R-Tree의 브랜치 노드, 각 도형의 객체(최하위 레벨 MBR)은 리프 노드에 저장. 
![[공간(T-Tree, Spatial) 인덱스 구조.png]]
- R-Tree의 인덱스 구조.

# 8.4.2 R-Tree 인덱스의 용도
- R-Tree는 앞에서 언급한 MBR 정보를 이용해 B-Tree 형태로 인덱스를 구축하므로 Rectangle의 'R'과 B-Tree의 "Tree"를 섞어 R-Tree라고 부른다.
	- 공간(Spatial) 인덱스라고도 한다. 
- 일반적으로는 WGS84(GPS) 기준의 위도, 경도 좌표 저장에 주로 사용된다. 
	- CAD/CAM 소프트웨어 또는 회로 디자인 등과 같이 좌표 시스템에 기반을 둔 정보에 대해서는 모두 적용할 수 있다. 
- R-Tree는 각 도형(의 MBR)의 포함 관계를 이용해 만들어진 인덱스다. 
	- 따라서 ST_Contains() 또는 ST_Within() 등과 같은 포함 관계를 비교하는 함수로 검색을 수행하는 경우에만 인덱스를 사용할 수 있다. 
		- 대표적으로 '현재 사용자의 위치로부터 반경 5km 이내의 음식점 검색' 등과 같은 검색에 사용할 수 있음.
	- 현재 출시되는 버전의 MySQL에서는 거리를 비교하는 ST_Distance()와 ST_Distance_Sphere() 함수는 공간 인덱스를 효율적으로 사용하지 못한다.
		- 때문에 공간 인덱스를 사용할 수 있는 ST_Contains() 또는 ST_Within()을 이용해 거리 기반 검색을 해야 한다. 
			- 현재도 그런듯
				- 네, 그 설명은 여전히 유효합니다. [MySQL에서는 `ST_Distance()`와 `ST_Distance_Sphere()` 함수가 공간 인덱스를 효율적으로 사용하지 못하므로, 대신 `ST_Contains()`나 `ST_Within()`과 같은 함수를 사용하여 거리 기반 검색을 수행하는 것이 좋습니다](https://velog.io/@noh0907/MySQL-%EC%A2%8C%ED%91%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-Spatial-Index-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0)[1](https://velog.io/@noh0907/MySQL-%EC%A2%8C%ED%91%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-Spatial-Index-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0)[2](https://velog.io/@kekim20/MySQL-%EB%B0%98%EA%B2%BD-n-km%EB%82%B4-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A1%B0%ED%9A%8C-%EA%B3%B5%EA%B0%84-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%A0%81%EC%9A%A9%EC%8B%9C-%EC%A3%BC%EC%9D%98%ED%95%A0-%EC%A0%90)[3](https://velog.io/@sdsd0908/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B0%98%EA%B2%BD-%EA%B2%80%EC%83%89-%EA%B8%B0%EB%8A%A5-DB-%EB%B3%80%EA%B2%BD%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EB%AF%BC). [이러한 함수들은 공간 인덱스를 활용하여 특정 거리 내에 있는 객체를 더 빠르게 찾을 수 있습니다](https://velog.io/@noh0907/MySQL-%EC%A2%8C%ED%91%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-Spatial-Index-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0)[1](https://velog.io/@noh0907/MySQL-%EC%A2%8C%ED%91%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-Spatial-Index-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0)[2](https://velog.io/@kekim20/MySQL-%EB%B0%98%EA%B2%BD-n-km%EB%82%B4-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A1%B0%ED%9A%8C-%EA%B3%B5%EA%B0%84-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%A0%81%EC%9A%A9%EC%8B%9C-%EC%A3%BC%EC%9D%98%ED%95%A0-%EC%A0%90)[3](https://velog.io/@sdsd0908/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B0%98%EA%B2%BD-%EA%B2%80%EC%83%89-%EA%B8%B0%EB%8A%A5-DB-%EB%B3%80%EA%B2%BD%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EB%AF%BC). [따라서, 공간 인덱스를 활용하려면 `ST_Contains()`나 `ST_Within()`과 같은 함수를 사용하는 것이 좋습니다](https://velog.io/@noh0907/MySQL-%EC%A2%8C%ED%91%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-Spatial-Index-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0)[1](https://velog.io/@noh0907/MySQL-%EC%A2%8C%ED%91%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-Spatial-Index-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0)[2](https://velog.io/@kekim20/MySQL-%EB%B0%98%EA%B2%BD-n-km%EB%82%B4-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A1%B0%ED%9A%8C-%EA%B3%B5%EA%B0%84-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%A0%81%EC%9A%A9%EC%8B%9C-%EC%A3%BC%EC%9D%98%ED%95%A0-%EC%A0%90)[3](https://velog.io/@sdsd0908/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B0%98%EA%B2%BD-%EA%B2%80%EC%83%89-%EA%B8%B0%EB%8A%A5-DB-%EB%B3%80%EA%B2%BD%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EB%AF%BC).
![[8.24특정 지점을 기준으로 가각 박스 이내의 위치를 검색.png]]
- 기준점 P로부터 반경 거리 5km 이내의 점(위치)들을 검색하는 경우
	- 사각 점선의 상자에 포함되는(ST_Contain() 또는 ST_Within() 함수 이용) 점들을 검색하면 됨.
		- 여기서 ST_Contain()나 ST_Within() 연산은 사각형 박스와 같은 다각형(Polygon)으로만 연산을 할 수 있으므로 반경 5km를 그리는 원을 포함하는 최소 사각형(MBR)으로 포함 관계 비교를 수행한 것.
```
--// ST_Contains() 또는 ST_Within()을 이용해 "사각 상자"에 포함된 좌표 Px만 검색

mysql> SELECT * FROM tb_location
	WHERE ST_Contains(사각 상자, px);

mysql> SELECT * FROM tb_location
	WHERE ST_Within(px, 사각 상자);
```
- ST_Contains() 함수와 ST_Within() 함수는 거의 동일한 비교를 수행하지만, 두 함수의 파라미터는 반대로 사용해야 한다. ST_Contains() 함수는 (MBR, 포함되는 도형)의 파라미터 구조, ST_Within()함수는 반대인 (포함되는 도형, MBR)의 파라미터 구조
- 상기 케이스에서 P6(5km 반등원에서는 배제되고, MBR에는 포함되어 있는 점)을 빼고 결과를 조회하려면 조금 더 복잡한 비교 필요.
	- ST_Contains() 비교의 결과에 대해 ST_Distance_Sphere() 함수를 이용해 다시 한번 필터링
```
mysql> SELECT * FROM tb_location
	WHERE ST_Contains(사각상자, px) --// 공간 좌표 Px가 사각 상자에 포함되는지 비교
		AND ST_Distance_Sphere(p, px)<=5*1000 /* 5km */;
```