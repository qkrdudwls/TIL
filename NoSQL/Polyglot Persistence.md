# Polyglot Persistence
* 다양한 데이터베이스는 각기 다른 문제를 해결하기 위해 설계되었고, 모든 요구 사항에 단일 데이터베이스 엔진을 사용하는 것은 비효율적이다.
    * 트랜잭션 데이터를 저장하고, 세션 정보를 caching 하며, 고객과 그 친구들이 구매한 제품의 관계를 탐색하는 것은 본질적으로 서로 다른 문제이다.
* RDBMS 내에서도 OLAP(온라인 분석 처리)와 OLTP(온라인 트랜잭션 처리) 시스템의 요구 사항은 매우 다르지만, 종종 동일한 스키마에 강제 통합된다.
* 데이터 관계를 생각해보면, RDBMS는 관계가 존재하도록 강제하는 것이 유리하다. 그러나 만약 관계를 발견하거나, 또는 동일한 객체에 속하는 서로 다른 테이블의 데이터를 찾아야 할 경우 RDBMS 사용이 어려워진다. 
* 데이터베이스 엔진은 특정 데이터 구조와 특정 작업을 잘 수행하도록 설계되었다.
## Disparate Data Storage Needs
* 많은 기업들이 비즈니스 거래, 세션 관리 데이터, reporting, BI, 데이터 warehousing 또는 logging 정보와 같은 다양한 요구 사항을 위해 동일한 데이터베이스 엔진을 사용하는 경우가 있다. 
* 2006년 Neal Ford는 **Polyglot Programming**이라는 용어를 만들어, 서로 다른 문제를 해결하기 위해 **다양한 언어를 사용하여 애플리케이션을 작성**해야 한다는 의견을 제시했다. 
* 복잡한 애플리케이션은 다양한 유형의 문제를 초래하기 때문에 **각 작업에 적합한 언어를 선택**하는 것이 모든 문제를 단일 언어로 해결하는 것보다 더 효과적일 수 있다.
* 혼합 저장 접근 방식을 정의하기 위해 **Polyglot Persistence**라는 용어를 사용한다.
    * 예를 들어, E-commerce 비즈니스 문제를 다룰 때, 쇼핑 카트를 위해 고가용성과 확장성을 갖춘 데이터 저장소를 사용하는 것이 중요하지만, 동일한 데이터 저장소는 고객의 친구들이 구매한 제품을 찾는 데 도움이 되지 않는다. 
## Polyglot Data Store Usage
* 위의 E-commerce 예시에 polyglot persistence 접근 방식을 적용해 보면, Key-Value 데이터베이스는 주문이 고객에 의해 확정되기 전의 쇼핑 카트 데이터를 저장하고, 세션 데이터를 저장하는 데 사용할 수 있다. 
    * 이렇게 하면 RDBMS가  일시적인 데이터를 처리하지 않아도 되는데, 쇼핑 카트는 일반적으로 사용자 ID로 접근되므로, Key-Value 데이터베이스가 적합하다. 한편, 주문이 확정되고 결제되면 RDBMS에 저장될 수 있는데, 마찬가지로 세션 데이터는 세션 ID로 키가 설정된다.
    * 고객이 쇼핑 카트에 제품을 담을 때 제품 추천 기능을 제공하려면, 그래프 데이터베이스를 도입하는 것이 적합하다. 
* 애플리케이션이 모든 요구 사항에 대해 단일 데이터베이스를 사용할 필요는 없다. **서로 다른 데이터베이스는 각기 다른 목적을 위해 설계**되었으며, **모든 문제를 단일 데이터베이스로 해결할 수는 없다.**
* 같은 애플리케이션 내에서도 데이터 warehousing 장치나 분석 장치와 같은 특화된 관계형 데이터베이스를 사용하는 것 또한 polyglot persistence로 볼 수 있다. 
## Service Usage over Direct Storage Usage
* 애플리케이션에서 여러 데이터베이스를 사용하게 되면, 기업 내 다른 애플리케이션들도 이러한 데이터베이스나 그 안에 저장된 데이터의 혜택을 볼 수 있다.
각 애플리케이션이 그래프 데이터베이스와 독립적으로 통신하는 대신, 그래프 데이터베이스를 이용하여 **모든 노드 간의 관계를 한 곳에 저장**하고 **모든 애플리케이션이 이를 조회**할 수 있도록 할 수 있다. 
    * 이렇게 하면 서비스 내부의 데이터베이스가 업데이트 되더라도 종속 애플리케이션을 변경할 필요가 없다. 
    * Riak와 Neo4J와 같은 많은 NoSQL 데이터베이스들은 기본적으로 REST API를 제공한다.
## Expanding for Better Functionality
* 성능 향상을 위해 **caching 기능을 추가**하거나, 검색을 더 효율적으로 만들기 위해 Solr와 같은 **인덱싱 엔진을 사용**할 수 있다. 
    * 이러한 기술을 도입할 때, 애플리케이션의 데이터베이스와 cache 또는 인덱싱 엔진 간의 데이터가 동기화되도록 해야 한다.
* 애플리케이션 데이터베이스의 데이터가 변경될 때 인덱싱된 데이터도 업데이트 해야 한다. 데이터 업데이트 과정은 **실시간**으로 이루어지거나 또는 **batch 방식**으로 수행될 수 있으며, 애플리케이션이 인덱스/검색 엔진의 **stale data를 처리**할 수 있어야 한다. **Event Sourcing 패턴**을 사용하여 인덱스를 업데이트 할 수 있다.
## Choosing the Right Technology
* 고객의 쇼핑 카트에 있는 제품과 관련하여 다른 고객들이 구매한 제품을 기반으로 제품을 추천하려면, 올바른 속성으로 데이터를 저장하여 이러한 쿼리에 응답할 수 있어야 한다. 
* 핵심은 올바른 기술을 사용하는 것이며, **쿼리가 변경되더라도 동일한 데이터베이스에서 기존 데이터를 잃거나 새로운 형식으로 변경하지 않고도 여전히 쿼리**할 수 있어야 한다.
* RDBMS를 사용하여 **계층적 쿼리**와 이에 맞는 **테이블 모델링**으로 해결할 수 있다. 
    * 탐색을 변경해야 할 때는 데이터베이스를 refactoring 하고 데이터를 migration 하며 새로운 데이터를 저장하기 시작해야 한다. 
* 노드 간의 관계를 추적하는 데이터베이스를 사용했다면, **새로운 관계를 프로그래밍** 하여 최소한의 변경으로 동일한 데이터베이스를 계속 사용할 수 있다.
## Enterprise Concerns with Polyglot Persistence
* DBA들은 NoSQL 기술이 어떻게 작동하는지 알아야 하고, 이러한 시스템을 모니터링하고 백업하며 데이터를 추출하고 넣는 방법을 배워야 한다. 
* NoSQL 기술은 **오픈 소스**이며 활발한 지원 커뮤니티가 있다. 또한 상업적 지원을 제공하는 회사도 있다.
    * 도구 공급 업체와 오픈 소스 커뮤니티는 MongoDB Monitoring Service, Datastax Ops Center, Riak용 Rekon 브라우저와 같은 도구를 출시하며 발전하고 있다.
* 기업이 우려하는 다른 영역은 **데이터 보안**으로, 데이터베이스 수준에서 사용자를 생성하고 권한을 할당하여 데이터를 볼 수 있거나 볼 수 없도록 하는 기능이다. 
    * 전통적인 RDBMS에서는 데이터가 데이터베이스에 의해 제공되었고, 어떤 쿼리 도구를 사용하더라도 데이터베이스에 접근할 수 있다.
    * NoSQL 데이터베이스에도 쿼리 도구가 있지만, 애플리케이션이 데이터를 소유하고 서비스를 통해 데이터를 제공하는 방식이 더 적합하다. 이러한 접근 방식에서는 **애플리케이션이 보안의 책임**을 지게 되는데, 물론 보안 기능을 도입하는 NoSQL 기술도 있다.
* 기업은 ETL 도구 또는 데이터 소스 시스템에서 데이터 warehouse로 데이터를 이동시키기 위해 사용하는 메커니즘이 NoSQL 데이터베이스의 데이터를 읽을 수 있도록 보장해야 한다.
## Deployment Complexity
* 애플리케이션에 polyglot persistence를 사용하기 시작하면 **배포 복잡성에 대한 고려가 필요**하다.
* 애플리케이션은 모든 데이터베이스가 **동시에 production 환경**에 있어야 하고, 이 데이터베이스를 UAT, QA 및 개발 환경에도 배포해야 한다. 
* 대부분의 NoSQL 제품은 오픈 소스이므로 라이선스 비용에 대한 영향이 적다. 또한 **설치 및 구성 자동화를 지원**한다.
    * 데이터베이스를 설치하기 위해서는 아카이브를 다운로드하고 압축을 푸는 작업만 필요하며, 이는 curl 및 unzip 명령어를 사용하여 자동화할 수 있다. 이러한 데이터베이스는 합리적인 기본값을 제공하며 최소한의 구성으로 시작할 수 있다.
    