# More Details on Data Models
## Grpah Databases
* 대부분의 NoSQL 데이터베이스는 clustering 되어 실행되어야 하기 때문에 큰 레코드와 단순한 연결을 가진 aggregate 지향 데이터 모델을 사용
* 그래프 데이터베이스는 작은 레코드와 복잡한 상호 연결을 특징을 가지는데, 이는 sns, 제품 선호도, 자격 규칙 등 복잡한 관계로 구성된 데이터를 탐색하는 데 이상적 
*그래프 데이터베이스의 기본 모델은 노드가 edge(또는 arcs)로 연결된 단순한 구조
* 데이터 모델은 변형이 가능한데, 특히 노드와 edge에 데이터를 메커니즘이 다양함
    * FlockDB: 추가 속성을 위한 메커니즘 없이 노드와 edge만 존재
    * Neo4J: 노드와 edge에 Java 객체를 속성으로 첨부 가능
    * Infinite Graph: 내장 타입의 서브클래스로서 노드와 edge로 Java 객체 저장   
* 그래프 데이터베이스는 노드와 edge로 구성된 그래프를 구축한 후, 쿼리 작업을 통해 네트워크를 조회
* 관계형 데이터베이스는 외래 키를 사용하여 관계를 구현할 수 있지만 조인을 통해 탐색하는 작업이 매우 비쌀 수 있어 높은 연결성을 가진 데이터 모델에서는 성능이 저하
* 그래프 데이터베이스는 관계를 따라 탐색하는 작업을 매우 저렴하게 만드는데, 이는 주로 그래프데이터베이스가 **탐색 작업의 대부분을 쿼리 시간에서 삽입 시간으로 옮기기 때문**
## Schemaless Databases
* Schemaless 데이터베이스는 데이터를 쉽게 저장 가능
* 데이터 저장 방식 변경, 새로운 데이터 추가, 필요하지 않은 데이터 저장 중단 모두 쉽게 가능, 데이터의 손실을 걱정할 필요 X
* 데이터를 조작하는 코드에 대한 가정인 **암묵적인 스키마** 존재
* Schemaless 데이터베이스는 스키마를 데이터에 접근하는 응용 프로그램 코드로 이동시킴
* 여러 응용 프로그램이 동일한 데이터베이스에 접근하는 문제 해결
    * 데이터베이스 상호작용을 단일 응용 프로그램에 캡슐화하고, 웹 서비스로 다른 응용 프로그램과 통합
    * Aggregate의 다른 영역을 명확히 구분  
## Materialized Views
* NoSQL 데이터베이스는 **map-reduce**계산을 통해 materialized views 생성
* Materialized views는 자주 읽히지만 약간의 delay를 견딜 수 있는 데이터에 효과적 
* Materialized views를 구축하는 2가지 방법
    * **On-Demand Calculation** (요청 시 계산): 업데이트 빈도가 낮거나 읽기 작업이 쓰기 작업보다 빈번할 경우 유용 
    * **Incremental Update** (점진적 업데이트): Materialized Views의 읽기 빈도가 쓰기 빈도보다 더 많고, materialized views가 가능한 최신 상태로 유지되어야 할 때 유용 
* Materialized views는 동일한 aggregate 내에서 사용 가능
