# Aggregate Data Models
## NoSQL Data Models
1. Key-Value
2. Document
3. Column-Family
4. Graph

위의 4개의 모델 중 1~3은 aggregate orientation이라는 공통 특성을 가진다.
## Aggregate
#### 데이터 조작과 일관성 관리를 위한 단위
* 일반적으로 aggregate를 atomic operation으로 업데이트하고, data storage와의 통신을 aggregate 단위로 수행한다. 
* Aggregate 단위로 작업하면 클러스터에서 복제 및 샤딩을 자연스럽게 처리할 수 있다. 
* NoSQL 데이터베이스는 모든 aggreagate 데이터를 하나의 노드에 함께 저장하도록 보장한다. 
* NoSQL 데이터베이스는 ACID 트랜잭션을 지원하지 않는 대신, 단일 aggregate의 atomic manipulation을 지원함으로써 **원자성**이 보장되고, aggregate 단위 내에서 데이터의 **일관성**이 유지된다. 
## Key-Value Model
* Key-Value 데이터 모델에서는 데이터를 key와 value의 쌍으로 저장하고, aggregate는 key-value 쌍으로 표현된다.
* 데이터베이스는 value의 내부 구조를 이해하지 않고 **단순히 key와 관련된 전체 데이터를 취급**한다. 
* Key를 통해서만 value를 조회할 수 있고, **value의 일부분을 검색하거나 복잡한 쿼리를 실행할 수 없다.** 
## Document Model
* 각 문서는 key와 value의 쌍으로 구성되지만, value 부분이 **구조화된 데이터를 포함**할 수 있다. 
* Document 데이터베이스는 문서 내부 구조를 이해할 수 있고, 복잡한 쿼리 언어를 사용하여 문서 내의 **특정 필드 값을 기준으로 검색**할 수 있다.
* 문서의 구조가 고정되어 있지 않아, **다양한 형식의 데이터를 저장**할 수 있다. 
* 문서의 구조가 고정되어 있지 않아, 최적화를 위해 필요한 정보를 사전에 알 수 없기 때문에 데이터베이스는 **필드를 최적화하여 저장 및 검색하는 데 제한**이 있다. 
## Column-Family Model 
* 데이터가 행과 열로 구성된 **테이블 형태로 저장**된다.
    * **행(Row)**: 데이터의 기본 단위로, 하나의 key에 의해 식별된다.
    * **열(Column)**: Column Family 내의 개별 데이터 요소로, 각 열은 이름, 값, 타임스탬프를 가진다.
    * **Column Family**: 관련 있는 열들을 묶어 놓은 것이다. 하나의 행은 여러 column family를 가질 수 있다. 
* 데이터가 **구조화되어 저장**되기 때문에 데이터베이스가 aggregate(하나의 행) 내의 데이터를 더 효율적으로 관리하고 접근할 수 있게 한다. 
* 데이터베이스는 **column family를 기준으로 인덱스를 생성**할 수 있어, 특정 column family에 대한 검색이 더 빠르고 효율적이며, **최적화 작업**을 수행할 수 있게 한다. 
* 각 행은 독립적이고, 새로운 열이나 column family를 추가하는 것이 용이하기 때문에 **데이터 스키마가 변경될 때 유연성**을 제공한다. 