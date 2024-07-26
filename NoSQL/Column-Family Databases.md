# Column-Family Databases
## What Is a Column-Family Data Store?
* Cassandra, HBase, Hypertable, Amazon SimpleDB와 같은 Column-Family 데이터베이스는 **key가 값에 매핑되고 값이 여러 column-family로 그룹화된 데이터를 저장**할 수 있게 해준다.
* Column-Family 데이터베이스는 **데이터를 column-family 안에 행으로 저장**하며, 각 행은 행 key와 연관된 여러 칼럼을 가지고 있다. 이때, Column-Family는 함께 접근되는 데이터 그룹이다.
* Cassandra는 빠르고 쉽게 확장 가능하며 쓰기 작업이 cluster 전체에 분산된다. Cluster에는 Master 노드가 없기 때문에, 읽기와 쓰기 작업을 cluster의 어떤 노드에서나 처리할 수 있다. 
## Features
* Cassandra에서 기본 단위는 **column**이다.
* Cassandra column은 **이름-값 쌍**으로 구성되며, 여기서 **이름이 key로 작용**한다. 이 key-value 쌍 **각각은 단일 column**이며 **항상 타임스탬프 값과 함께 저장**된다.
* Column 데이터가 더 이상 사용되지 않으면, 압축 단계 동안 그 공간을 회수할 수 있다.
* Column-Family의 column이 단순한 column인 경우, 이 column-family를 **Standard Column-Family**라고 한다.
* Column-Family는 **key가 행을 식별**하고 **행은 여러 column으로 구성**되는데, RDBMS에서 컨테이너와 다른 점은 **다양한 행들이 동일한 column을 가질 필요가 없고**, 언제든지 **다른 행에 column을 추가하지 않고도 특정 행에 column을 추가할 수 있다**는 것이다. 
* Column이 column의 맵으로 구성되면 이를 **Super Column**이라고 한다. Super Column은 이름과 값(column의 맵)으로 구성되고, super column을 column의 컨테이너로 생각할 수 있다. 
* Super Column을 사용하여 column-family를 만들면 **Super Column-Family**가 된다. 
* Super Column-Family는 관련된 데이터를 함께 유지하는 데 유용하지만, 대부분의 경우 필요하지 않은 일부 column들이 Cassandra에 의해 여전히 fetch 되고, deserialized 되므로 최적의 성능을 내지 못할 수 있다. 
* Cassandra는 standard 및 super column-family를 **keyspace**에 넣는다. Keyspace는 RDBMS의 데이터베이스와 유사하여 애플리케이션과 관련된 모든 column-family를 할당할 수 있다. 
### Consistency
* Cassandra가 쓰기 작업을 받으면, 데이터가 먼저 커밋 로그에 기록된 후 메모리 구조인 **memtable**에 기록된다.
* 쓰기 작업은 커밋 로그와 memtable에 기록되면 성공한 것으로 간주된다.
* 쓰기는 메모리에 배치되고 주기적으로 **SSTable**이라는 구조에 사용된다. 
    * SSTable은 flush 된 후 다시 쓰이지 않으며, 데이터에 변경 사항이 있으면 새로운 SSTable이 작성된다.
    * 사용되지 않는 SSTable은 압축 단계에서 회수된다.
* 읽기 작업에서 일관성은 기본적으로 모든 읽기 작업에 대한 일관성 설정이 **ONE**인 경우, 읽기 요청이 있을 때 Cassandra는 **첫 번째 복제본에서 데이터를 반환**한다.
* 데이터가 오래된 경우, 후속 읽기에서 최신 데이터를 얻는데, 이를 **read repair**라고 한다.
* 쓰기 작업에서도 마찬가지로, Cassandra는 **한 노드의 커밋 로그에 쓰고 클라이언트에 응답**한다. 일관성 수준 ONE 은 매우 높은 쓰기 성능이 필요하고 일부 쓰기가 손실되어도 상관없는 경우에 적합하다. 노드가 다른 노드로 복제되기 전에 다운되면 쓰기가 손실될 수 있다.
* 읽기 및 쓰기 작업에 대해 **QUORUM 일관성 설정**을 사용하면 **다수의 노드가 응답**해야 하며 **최신 타임 스탬프의 column이 클라이언트에 반환**된다. 
    * 최신 데이터를 갖지 않은 복제본은 읽기 복구 작업을 통해 복구된다. 
    * 쓰기 작업에서는 QUORUM 일관성 설정으로 인해 다수의 노드에 쓰기가 전파되어야 성공으로 간주되고 클라이언트에게 알린다.
* 일관성 수준을 **ALL**로 설정하면 모든 노드가 읽기 또는 쓰기에 응답해야 하므로 **하나의 노드가 다운되더라도 쓰기나 읽기가 차단되고 실패로 보고**된다. 따라서 시스템 설계자는 애플리케이션 요구 사항에 따라 일관성 수준을 조정해야 한다. 
* Keyspace 생성 시 저장할 데이터 복제본 수를 구성할 수 있는데, 이 숫자는 **데이터의 복제 계수를 결정**한다. 
* 노드가 다운된 동안 해당 노드에 저장될 데이터는 다른 노드로 넘겨지고, 노드가 다시 온라인 상태가 되면 데이터 변경 사항이 노드에 반환된다. 이 기술을 **Hinted Handoff**라고 하며, 실패한 노드를 더 빠르게 복원할 수 있다. 
### Transactions
* 전통적인 의미의 트랜잭션은 없다.
* Cassandra에서 쓰기는 **행 수준에서 atomic**이며, 주어진 행 key에 대해 column을 삽입하거나 업데이트하는 작업은 단일 쓰기로 처리되어 성공하거나 실패한다.
* **외부 트랜잭션 라이브러리(ZooKeeper 등)를 사용하여 쓰기 및 읽기를 동기화**할 수 있다. ZooKeeper 위에 트랜잭션을 wrap 할 수 있는 Cages와 같은 라이브러리도 있다.
### Availability
* Cassandra는 cluster에 Master가 없고 모든 노드가 동등한 peer로 설계되어 있어 가용성이 높고, 요청의 일관성 수준을 낮춤으로써 cluster의 가용성을 높일 수 있다. 
### Query Features
* Cassandra에서 데이터 모델을 설계할 때 풍부한 쿼리 언어가 없으므로 데이터를 읽는 데 최적화된 column 및 column-family를 만드는 것이 좋다. 
* 각 행의 데이터는 column 이름에 따라 정렬된다. 특정 column을 다른 column보다 자주 검색하는 경우, 해당 값을 행 key로 사용하는 것이 성능 상 더 좋다.
#### Basic Queries
* Cassandra 클라이언트를 사용하여 실행할 수 있는 기본 쿼리에는 GET, SET, DEL이 있다. 
* 데이터 쿼리를 시작하기 전에 USE ecommerce; 명령을 실행하여 **모든 쿼리가 데이터를 넣은 keyspace에서 실행되도록** 해야 한다. Keyspace의 column-family를 사용하기 전에 이를 정의해야 한다.
* GET 명령을 사용하여 데이터를 다시 읽을 수 있다. 전체 column-family를 가져오거나, 필요한 column만 가져올 수 있다. 
    * 특정 column만 가져오는 것이 더 효율적인데, 데이터가 많은 column-family의 경우 특히 더 유용하다. 
* SET 명령을 사용하면 column을 새로운 값으로 설정하는 것으로, 데이터를 업데이트할 수 있다.
* DEL 명령을 사용하면 column 또는 전체 column-family를 삭제할 수 있다. 
#### Advanced Queries and Indexing
* Cassandra는 **key가 아닌 다른 column에 인덱스를 생성**할 수 있다.
* 인덱싱된 column에 대해 직접 쿼리할 수 있는데, 이 인덱스는 **bit-mapped 인덱스**로 구현되며, low-cardinality column 값에 대해 성능이 우수하다. 
#### Cassandra Query Language (CQL)
* Cassandra는 SQL과 유사한 명령을 지원하는 쿼리 언어인 Cassandra Query Language (CQL)를 가지고 있다. 
* CQL에는 데이터를 쿼리하는 많은 기능이 있지만, SQL이 제공하는 모든 기능을 제공하지는 않는다. 
* CQL은 조인이나 서브쿼리를 허용하지 않으며, WHERE 절은 일반적으로 단순하다.
### Scaling
* 기존 Cassandra cluster의 확장은 **노드를 추가하는 것으로 가능**하다.
* Cluster에 Master 노드가 없기 때문에 노드를 추가하면 더 많은 쓰기 및 읽기 작업을 지원할 수 있고, 이러한 수평 확장은 새로운 노드를  cluster에 추가하는 동안 cluster가 클라이언트의 요청을 계속 처리할 수 있게 하여 최대 가동 시간을 제공한다.
## Suitable Use Cases
### Event Logging
* Column-Family 데이터베이스는 다양한 데이터 구조를 저장할 수 있는 능력 덕분에 애플리케이션 상태 또는 오류와 같은 이벤트 정보를 저장하는 데 매우 적합하다. 
* 기업 내 모든 애플리케이션은 각자의 column을 사용하여 Cassandra에 이벤트를 기록할 수 있으며, 행 key는 appname:timestamp 형태로 지정할 수 있다. 
* Cassandra는 쓰기 확장이 가능하기 때문에 이벤트 로깅 시스템에 이상적이다.
### Content Management Systems, Blogging Platforms
* Column-Family를 사용하면 태그, 카테고리, 링크 및 트랙백과 함께 블로그 항목을 저장할 수 있다. 댓글은 같은 행에 저장하거나 다른 keyspace로 이동할 수 있다. 
* 마찬가지로 블로그 사용자와 실제 블로그는 다른 column-family에 저장할 수 있다. 이렇게 하면 데이터를 쉽게 확장하고 관리할 수 있다.
### Counters
* 웹 애플리케이션에서 방문자 수를 세고 분류하여 분석을 계산하는 경우가 자주 있다. 이때 column-family 생성 시 CounterColumnType을 사용할 수 있다.
* Column-Family를 생성한 후 웹 애플리케이션 내의 각 페이지 방문에 대해 임의의 column을 가질 수 있다.
### Expiring Usage
* 사용자에게 데모 액세스를 제공하거나 특정 시간 동안 웹사이트에 광고 배너를 표시하고자 할 때 만료되는 column을 사용할 수 있다. 
* Cassandra는 지정된 시간이 지난 후 자동으로 삭제되는 column을 허용한다. 이 시간을 TTL(Time To Live)이라 하며, 초 단위로 정의된다. TTL이 경과하면 column이 삭제되며, column이 존재하지 않으면 액세스를 철회하거나 배너를 제거할 수 있다.
## When Not to Use
* **ACID 트랜잭션이 필요한 시스템**: 쓰기 및 읽기에 대한 ACID 트랜잭션이 필요한 경우 Column-Family 데이터베이스는 적합하지 않다.
* **데이터 집계를 위한 쿼리**: 데이터를 집계하는 데 (예: SUM 또는 AVG) 데이터베이스가 필요하다면, 클라이언트가 모든 행에서 데이터를 가져와서 직접 처리해야 한다.
* **초기 프로토타입 또는 기술 스파이크**: 초기 단계에서는 쿼리 패턴이 어떻게 변화할지 알 수 없다. 쿼리 패턴이 변경됨에 따라 column-family 설계를 변경해야 하는데, 이는 제품 혁신 팀에 마찰을 일으키고 개발자 생산성을 저하시킬 수 있다. RDBMS는 스키마 변경에 높은 비용이 들지만 쿼리 변경 비용이 낮다. 반면에 Cassandra에서는 스키마 변경 비용이 낮지만 쿼리 변경 비용이 더 높을 수 있다.