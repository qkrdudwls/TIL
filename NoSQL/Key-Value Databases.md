# Key-Value Databases
## What is a Key-Value Store
* API 관점에서 가장 간단한 NoSQL 데이터베이스
* 클라이언트는 key에 대한 값을 가져오거나 (**get**), key에 대한 값을 설정하거나 (**put**), 데이터베이스에서 key를 삭제 (**delete**)할 수 있다.
* 값은 **blob** 형태로 저장, 데이터베스는 내부의 내용에 대해 알 필요가 없고, 이를 이해하는 것은 애플리케이션.
* Key-Value 데이터베이스는 항상 **primary key 접근 방식**을 사용한다.
* Key-Value 데이터베이스에서는 애플리케이션이 ID와 VALUE를 제공하면 이를 저장한다. 만약 ID가 이미 존재하면 현재 값이 덮어 쓰여지고, 존재하지 않으면 새로운 항목이 생성된다.
### 주요 Key-Value 데이터베이스
* **Riak**: Key를 bucket에 저장하는 기능을 제공, bucket은 key를 세분화하는 방법으로, key의 평면 namespace로 생각할 수 있다.
* **Redis**: 리스트, 셋, 해시 등의 데이터 구조를 저장할 수 있으며, 범위, 차이, 합집합 및 교집합 연산을 수행할 수 있다.
* **Memcached**: 주로 캐시로 사용되는 DB로 다양한 변형이 존재한다.
* **Berkeley DB**: 고성능 트랜잭션 처리 기능을 제공하는 임베디드 데이터베이스이다.
* **HamsterDB**: 임베디드 용도로 특히 적합한 데이터베이스이다.
* **Amazon DynamoDB**: 오픈 소스가 아닌 아마존의 DynamoDB 구현체이다.
* **Project Voldemort**: 아마존 DynamoDB의 오픈 소스 구현체이다.
## Key-Value Store Features
### Consistency
* 단일 key에 대한 작업(get, put, delete)에만 허용한다.
* **Optimistic writes**를 수행할 수 있지만 데이터베이스가 값을 변경할 수 없기 때문에 구현 비용이 많이 든다.
* Riak와 같은 분산 Key-Value 데이터베이스 구현에는 **최종 일관성** 모델이 구현된다. 값이 이미 다른 노드에 복제되어 있을 수 있기 때문에 Riak는 업데이트 충돌을 피하는 두 가지 방법을 제시한다.
    1. 최신 쓰기가 성공, 이전 쓰기는 실패
    2. 모든 값을 반환하여 클라이언트가 충돌 해결
* Riak에서는 위의 옵션을 bucket 생성 시 설정할 수 있는데, bucket 생성 시 일관성에 대한 기본 값을 제공할 수 있다.
    * 데이터가 저장된 모든 노드에서 데이터가 일관되게 저장될 때만 쓰기가 성공한 것으로 간주된다. 
    * 모든 노드의 데이터가 일관되도록 하려면 numberOfNodesToRespondToWrite를 w로 설정하여 nVal과 동일하게 만들 수 있다.
    * 쓰기 또는 읽기 충돌을 개선하려면 bucket 생성 시 allowSiblings 플래그를 변경할 수 있는데, 이를 false로 설정하면 마지막 쓰기가 성공하고 자식이 생성되지 않는다.
### Transacitons
* Key-Value 데이터베이스 종류마다 트랜잭션 사양이 다르다.
* 일반적으로 쓰기에 대한 보장은 없다.
* 많은 데이터베이스는 다양한 방식으로 트랜잭션을 구현하는데, Riak는 쓰기 API 호출 시 복제 요소 W 값을 사용하여 쿼럼 개념을 구현한다.
### Query Features
* 모든 Key-Value 데이터베이스는 key로 쿼리할 수 있다.
* 만약 값 열의 일부 속성을 사용하여 쿼리하려고 한다면, 이는 데이터베이스에서는 불가능하다.
* 디버깅 중 임시 쿼리를 할 때 key를 모른다면 일부 Key-Value 데이터베이스는 값 내부를 검색할 수 있는 기능을 제공하여 이를 해결한다.
    * Riak Search는 Lucene 인덱스를 사용하여 데이터를 쿼리할 수 있다. 
* Key-Value 데이터베이스를 사용할 때, key를 알고리즘으로 생성할 수 있는지, 사용자가 key를 제공할 수 있는지, 또는 타임스탬프나 데이터베이스 외부에서 파생된 다른 데이터로 key를 생성할 수 있는지 등을 고려해야 한다. 
* Riak bucket에 저장할 때, 저장 API를 사용하여 key에 대한 객체를 저장한다. 또한 fetch API를 사용하여 key에 저장된 값을 가져올 수 있다. Riak는 HTTP 기반 인터페이스를 제공하므로, 웹 브라우저 또는 명령줄에서 curl을 사용하여 모든 작업을 수행할 수 있다.
### Structure of Data
* Key-Value 데이터베이스는 key-value 쌍의 값 부분에 무엇이 저장되는지 신경쓰지 않는다.
* **값은 blob, 텍스트, JSON, XML 등 어떤 것이든 될 수 있다.**
* Riak에서는 POST 요청 시 **Content-Type을 사용**하여 데이터 타입을 지정할 수 있다.
### Scaling
* 많은 Key-Value 데이터베이스는 **샤딩**을 통해 확장성을 확보한다.
* 샤딩은 몇 가지 문제를 야기하는데, 만약 f로 시작하는 key를 저장하는 노드가 다운되면, 해당 노드에 저장된 데이터는 사용할 수 없게 되며, f로 시작하는 데이터는 쓸 수 없다.
* Riak와 같은 데이터베이스는 **CAP 정리를 제어**할 수 있도록 허용한다.
    * 5 노드 Riak cluster가 있다고 가정해 보자. N을 3으로 설정하면 모든 데이터는 최소한 세 개의 노드에 복제되고, R을 2로 설정하면 두 개의 노드가 GET 요청에 응답해야 성공으로 간주되며, W를 2로 설정하면 PUT 요청이 두 개의 노드에 쓰여진 후에야 쓰기가 성공으로 간주된다.
    * 위와 같은 설정은 읽기 또는 쓰기 작업에 대한 노드 장애를 세밀하게 조정할 수 있다.
    * 필요에 따라 이러한 값을 변경하여 읽기 가용성 또는 쓰기 가용성을 개선할 수 있다.
## Suitable Use Cases
### Storing Session Information
* 애플리케이션 세션 ID를 디스크나 RDBMS에서 Key-Value 데이터베이스로 이동시키면 큰 이점을 얻을 수 있는데, 세션에 관한 모든 것은 단일 PUT 요청으로 저장되거나 GET을 사용하여 검색할 수 있기 때문이다.
* 단일 요청 작업은 **세션에 관한 모든 것이 단일 객체에 저장**되므로 속도가 매우 빠르다.
* Memacached와 같은 solution이 널리 사용되며, Riak는 가용성이 중요한 경우에 사용할 수 있다.
### User Profiles, Preferences
* 사용자에 관한 **모든 정보를 하나의 객체에 저장**하여 단일 GET 작업으로 가져올 수 있다.
* 이와 비슷한 맥락으로 Product Profiles 또한 가능하다.
### Shopping Cart Data
* E-commerce 웹사이트에서는 쇼핑 카트를 사용자에 연결하는데, 모든 쇼핑 정보를 key로 사용자 ID를 사용하여 값에 저장할 수 있다.
* Riak cluster는 이러한 응용 프로그램에 적합하다.
## When Not to Use
### Relationships among Data
* 서로 다른 데이터 세트 간의 관계를 가져야 하거나, 서로 다른 key간의 데이터를 연관시켜야 하는 경우, Key-Value 데이터베이스는 적합하지 않다.
### Multioperation Transactions
* 여러 key를 저장하고 그 중 하나라도 저장에 실패하면 나머지 작업을 되돌려야 하는 경우, Key-Value 데이터베이스는 적합하지 않다.
### Query by Data
* Key-Value 쌍의 값 부분에서 무언가를 검색해야 하는 경우, Key-Value 데이터베이스는 적합하지 않다. 
* Riak Search나 Lucene과 같은 인덱싱 엔진을 제외하면 데이터베이스에서 값을 검사할 방법이 없다.
### Operations by Sets
* 작업이 한 번에 하나의 key로 제한되므로 여러 key에 대해 작업할 방법이 없다. 
* 여러 key에 대해 작업해야 하는 경우 클라이언트 측에서 처리해야 한다.