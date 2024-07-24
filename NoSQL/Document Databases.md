# Document Databases
## What Is a Document Databases?
* 문서 데이터베이스에서는 데이터의 스키마는 문서마다 다를 수 있지만, 이 문서들은 같은 컬렉션에 속할 수 있다.
    *  문서는 XML, JSON, BSON 등 다양한 형식을 가질 수 있다.
    * 문서는 Self-describing하는 성격을 가진다.
    * 데이터베이스는 계층적 트리 구조이다.
    * Map, Collection, Scalar 값 등으로 구성된다.
* 문서 데이터베이스는 비어 있는 속성이 없으며, 특정 속성이 없으면 해당 속성이 설정되지 않았거나 문서와 관련이 없다고 가정한다.
* 새로운 속성을 정의하거나 기존 문서를 변경할 필요 없이 문서를 추가할 수 있다.
* **주요 Document Dataabases**: MongoDB, CouchDB, Terrastore, OrientDB, RavenDB, Lotus Notes
## Features (MongoDB)
* 각 MongoDB는 여러 데이터베이스를 가지고 잇으며, 각 데이터베이스는 여러 컬렉션을 가질 수 있다.
    * MongoDB의 인스턴스 = RDBMS의 인스턴스
    * MongoDB의 데이터베이스 = RDBMS의 스키마
    * MongoDB의 컬렉션 = RDBMS의 테이블
* 문서를 저장할 때는 문서가 속할 데이터베이스와 컬렉션을 선택해야 한다.
    * database.collection.insert(document)로 표현
### Consistency
* MongoDB 데이터베이스에서 일관성은 **replica 세트를 사용**하여 구성된다.
* 
