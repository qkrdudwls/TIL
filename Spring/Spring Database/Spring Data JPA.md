# Spring Data JPA

Spring Data JPA는 스프링 프레임워크에서 제공하는 **데이터 접근 계층을 구현하기 위한 모듈** 중 하나로, JPA 기반의 데이터베이스 작업을 쉽게 할 수 있도록 도와준다. Spring Data JPA는 표준 JPA 기능 외에도 다양한 기능을 제공하여 애플리케이션 개발 시 **반복적인 코드 작성을 줄이고, 데이터 접근 계층을 보다 효율적으로 관리**할 수 있도록 지원한다.

## JPA

JPA(Java Persistence API)는 자바 객체와 데이터베이스 사이의 매핑을 관리하기 위한 표준 API이다. JPA를 사용하면 **데이터베이스 테이블과 자바 클래스 간의 매핑을 정의**하고, **데이터를 자바 객체로 관리**할 수 있게 된다. JPA는 기본적으로 데이터베이스 테이블의 행을 자바 객체로 표현하며, **SQL 쿼리를 사용하지 않고도 데이터베이스 작업을 수행**할 수 있다. JPA는 **데이터베이스 독립성을 제공**하여 데이터베이스 벤더에 의존하지 않는 코드를 작성할 수 있도록 도와준다.

## Features

Spring Data JPA는 JPA의 기능을 더 편리하게 사용할 수 있도록 추가 기능을 제공한다. 주요 기능들은 다음과 같다.

- **리포지토리 기반 개발(Repository-based Development)**
- **쿼리 메소드 자동 생성(Query Method Creation)**
- **JPQL(Java Persistence Query Language) 및 네이티브 쿼리 지원(Native Query Support)**
- **페이징 및 정렬 지원(Paging and Sorting)**
- **Auditing(감사) 기능**

### Repository-based Development

Spring Data JPA에서 가장 핵심적인 개념 중 하나는 리포지토리 인터페이스를 통한 데이터 접근 방식이다. 이 방식은 개발자가 데이터 접근을 위한 코드를 일일이 작성하지 않아도 되도록 도와준다.

#### Principle

Spring Data JPA의 리포지토리 인터페이스는 일반적으로 **`JpaRepository<T, ID>` 를 상속받아 사용**한다. 이 인터페이스는 기본적인 CRUD 연산을 비롯한 여러 데이터 접근 메서드를 미리 정의해 둔 인터페이스이다.

- **프록시(Proxy) 패턴**: Spring Data JPA는 런타임 시점에 개발자가 정의한 인터페이스에 대한 프록시 객체를 생성한다. 이 프록시 객체는 인터페이스에 선언된 메서드 호출을 가로채어 JPA 엔티티 매니저를 사용하여 데이터베이스 작업을 수행한다.
- **AOP**: 스프링의 AOP(Aspect-Oriented Programming): 스프링의 AOP 기능을 활용하여 선언적 트랜잭션 관리나 리포지토리 메서드의 동적 구현을 제공한다. 이를 통해 리포지토리 인터페이스에 선언된 메서드를 호출할 때 필요한 트랜잭션 시작, 커밋, 롤백 등을 자동으로 처리할 수 있다.

#### Example

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // 기본 생성자, getter, setter
}
```

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    // 기본적인 CRUD 메서드들은 이미 정의되어 있다.
}
```

### Query Method Creation

쿼리 메서드 자동 생성은 **메서드 이름을 기반으로 JPA 쿼리를 자동으로 생성하고 실행**하는 기능이다.

#### Principle

Spring Data JPA는 메서드 이름을 분석하여 적절한 JPQL 쿼리를 생성하는 규칙을 내장하고 있다.

- **메서드 이름 파싱**: Spring Data JPA는 메서드 이름을 분석하여 메서드의 의미를 파악한다. 예를 들어, `findByNameAndEmail` 이라는 메서드는 `find` 와 `ByNameAndEmail` 을 조합한 것으로 이해한다.
- **JPQL 쿼리 생성**: 메서드 이름에서 파싱한 정보에 따라 JPQL 쿼리를 동적으로 생성한다. `findByNameAndEmail` 메서드는 다음과 같이 JPQL 쿼리로 변환된다.
    
    ```sql
    SELECT u FROM User u WHERE u.name = ?1 AND u.email = ?2
    ```
    
- **매개변수 바인딩**: 메서드에 전달된 파라미터들은 JPQL 쿼리의 매개변수로 바인딩된다.
- **동적 쿼리 실행**: 생성된 JPQL 쿼리가 JPA의 `EntityManager` 에 의해 실행되고, 결과가 반환된다.

#### Example

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    
    // 이름으로 사용자 검색
    List<User> findByName(String name);

    // 이메일로 사용자 검색
    User findByEmail(String email);

    // 이름과 이메일로 사용자 검색
    List<User> findByNameAndEmail(String name, String email);
}
```

### JPQL and Native Query Support

Spring Data JPA는 JPQL 뿐만 아니라 네이티브 SQL 쿼리도 지원한다.

#### Principle

- **JPQL 지원**: JPQL은 SQL과 비슷하지만, 엔티티 객체에 대한 쿼리를 정의하기 위한 객체 지향 쿼리 언어이다. Spring Data JPA는 `@Query` 어노테이션을 사용하여 JPQL 쿼리를 직접 작성할 수 있도록 지원한다. 이때 JPA는 JPQL 쿼리를 SQL로 변환하여 실행한다.
- **네이티브 쿼리 지원**: `@Query` 애노테이션에서 `nativeQuery = true` 속성을 사용하면 네이티브 SQL 쿼리를 작성할 수 있다. 네이티브 쿼리는 데이터베이스 고유의 기능을 사용해야 할 때 유용하며, JPA는 이 쿼리를 그대로 데이터베이스에 전달하여 실행한다.

#### Example

- JPQL
    
    ```java
    import org.springframework.data.jpa.repository.Query;
    import org.springframework.data.repository.query.Param;
    import java.util.List;
    
    public interface UserRepository extends JpaRepository<User, Long> {
    
        @Query("SELECT u FROM User u WHERE u.name = :name")
        List<User> findUsersWithName(@Param("name") String name);
    }
    ```
    
- Native Query
    
    ```java
    import org.springframework.data.jpa.repository.Query;
    import org.springframework.data.repository.query.Param;
    import java.util.List;
    
    public interface UserRepository extends JpaRepository<User, Long> {
    
        @Query(value = "SELECT * FROM user WHERE name = :name", nativeQuery = true)
        List<User> findUsersWithNameNative(@Param("name") String name);
    }
    ```
    

### Paging and Sorting

페이징 및 정렬 기능은 대량의 데이터를 효율적으로 조회하기 위한 기능으로, **`Pageable` 인터페이스와 `Page` 객체를 통해 페이징 처리를 지원**한다.

#### Principle

- **`Pageable` 인터페이스**: 페이징과 정렬 정보를 담고 있는 객체로, 요청 페이지 번호(page number), 페이지 크기(page size), 정렬 기준(sort order) 등을 포함한다.
- **`Page` 인터페이스**: 실제 조회된 데이터와 함께 페이징 정보를 담고 있는 객체로, 전체 페이지 수(total pages), 전체 요소 수(total elements), 현재 페이지 데이터 등 페이징 관련 정보를 제공한다.
- **쿼리 최적화**: Spring Data JPA는 페이징 요청에 따라 `LIMIT`과 `OFFSET`을 사용하여 SQL 쿼리를 최적화 한다. 예를 들어, `findByName(String name, Pageable pageable)` 메소드는 이름으로 필터링된 결과를 요청한 페이지 크기와 페이지 번호에 따라 적절히 분할하여 반환한다.

#### Example

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {

    // 페이징 처리를 통해 사용자 목록을 가져옴
    Page<User> findByName(String name, Pageable pageable);
}
```

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public Page<User> getUsersByName(String name, int page, int size) {
        return userRepository.findByName(name, PageRequest.of(page, size));
    }
}
```

### Auditing

감사 기능은 엔티티가 생성되거나 수정될 때 **자동으로 타임스탬프와 사용자 정보를 저장**하는 기능이다.

#### Principle

- **`@EnableJpaAuditing` 어노테이션**: 이 어노테이션을 사용하여 JPA의 감사 기능을 활성화한다. 감사 기능이 활성화되면, 스프링이 자동으로 엔티티의 생성 및 수정 이벤트를 감지한다.
- **감사 필드**: 엔티티 클래스에 `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy` 어노테이션을 사용하여 필드를 정의한다. 이러한 필드는 JPA의 영속성 컨텍스트에 의해 엔티티가 생성되거나 수정될 때 자동으로 업데이트된다.
- **감사 이벤트 리스너**: Spring Data JPA는 내부적으로 감사 이벤트 리스너를 사용하여 엔티티의 생명주기 이벤트(`@PrePersist`, `@PreUpdate` 등)를 가로챈다. 이를 통해 감사 필드가 자동으로 업데이트된다.

#### Example

```java
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.time.LocalDateTime;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;

    // getter, setter, 생성자
}
```

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@SpringBootApplication
@EnableJpaAuditing
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

## Advantages

- **코드 간소화**: 기본적인 CRUD 작업과 복잡한 쿼리를 쉽게 작성할 수 있으며, 반복적인 코드 작성이 줄어든다.
- **생산성 향상**: 비즈니스 로직에 집중할 수 있으며, 데이터베이스 작업을 보다 빠르게 개발할 수 있다.
- **유연성**: 다양한 데이터베이스와 연동이 가능하며, JPA 표준을 따르기 때문에 ORM의 교체도 용이하다.
- **유지보수성**: 데이터 접근 계층이 간결해지므로 유지보수와 테스트가 용이하다.