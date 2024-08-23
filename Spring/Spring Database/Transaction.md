# Transaction

트랜잭션은 **데이터베이스에서 실행되는 작업의 논리적인 단위**이다. 트랜잭션은 주로 다수의 데이터베이스 작업을 묶어 **하나의 작업처럼 처리**하며, **ACID 속성을 만족**해야 한다.

- **Atomicity**: 트랜잭션 내의 모든 작업이 성공하거나 모두 실패해야 한다. 즉, 트랜잭션 중 하나의 작업이 실패하면 전체 트랜잭션이 취소된다.
- **Consistency**: 트랜잭션이 완료된 후 데이터베이스의 상태는 일관된 상태를 유지해야 한다.
- **Isolation**: 여러 트랜잭션이 동시에 수행될 때, 서로 간섭하지 않아야 한다.
- **Durability**: 트랜잭션이 성공적으로 완료되면 그 결과가 영구적으로 반영되어야 한다.

## Transaction Management

스프링 프레임워크는 다양한 데이터 소스와 ORM 프레임워크에 대해 일관된 트랜잭션 관리 기능을 제공한다. 스프링의 트랜잭션 관리 기능은 크게 두 가지 방법으로 사용할 수 있다.

1. **선언적 트랜잭션 관리 (Declarative Transaction Management)**
2. **프로그래밍적 트랜잭션 관리 (Programmatic Transaction Mangement)**

### Declarative Transaction Management

선언적 트랜잭션 관리는 **어노테이션이나 XML 설정을 사용하여 트랜잭션을 관리**하는 방법이다. 이 방법은 비즈니스 로직 코드에 트랜잭션 관리 코드를 삽입하지 않고도 트랜잭션을 쉽게 적용할 수 있게 해준다. 일반적으로 프로그래밍적 트랜잭션 관리보다 더 많이 사용되며, **유지 보수가 쉽고 가독성이 높은 코드 작성이 가능**하다.

#### Settings

- **어노테이션 기반**: `@Transactional` 어노테이션을 사용하여 트랜잭션을 적용할 수 있다.
- **XML 기반**: XML 설정 파일을 사용하여 트랜잭션 관리를 구성할 수 있다.

#### Example

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class UserService {

    @Transactional
    public void createUser(User user) {
        // 사용자 정보 저장 로직
        userRepository.save(user);
        
        // 사용자 역할(Role) 저장 로직
        userRoleRepository.save(user.getRole());
        
        // 이 메서드 내의 모든 작업은 트랜잭션으로 묶인다.
    }
}
```

위의 예제에서 `@Transactional` 어노테이션이 적용된 `createUser` 메서드는 하나의 트랜잭션으로 실행된다. 만약 메서드 내에서 예외가 발생하면, 해당 트랜잭션이 롤백되고 모든 작업이 취소된다.

#### Propagation Options

트랜잭션 전파 옵션은 트랜잭션이 이미 존재하는지 여부에 따라 새로운 트랜잭션을 시작할 지, 기존 트랜잭션에 참여할 지를 결정한다. 스프링에서 제공하는 전파 옵션은 다음과 같다.

- **REQUIRED**: 기본 전파 속성이다. 현재 트랜잭션이 존재하면 해당 트랜잭션에 참여하고, 없으면 새로운 트랜잭션을 시작한다.
- **REQUIRES_NEW**: 항상 새로운 트랜잭션을 시작한다. 이미 트랜잭션이 존재하면 기존 트랜잭션을 일시 정지한다.
- **MANDATORY**: 반드시 기존 트랜잭션이 존재해야 한다. 그렇지 않으면 예외가 발생한다.
- **SUPPORTS**: 트랜잭션이 존재하면 트랜잭션 내에서 실행하고, 없으면 트랜잭션 없이 실행한다.
- **NOT_SUPPORTED**: 트랜잭션 없이 실행하며, 기존 트랜잭션이 존재하면 일시 정지한다.
- **NEVER**: 트랜잭션 없이 실행해야 하며, 트랜잭션이 존재하면 예외가 발생한다.
- **NESTED**: 이미 트랜잭션이 존재하면 중첩된 트랜잭션으로 실행하고, 그렇지 않으면 새로운 트랜잭션을 시작한다.

### Programmatic Transaction Management

프로그래밍적 트랜잭션 관리는 **`TransactionTemplate` 또는 `PlatformTransactionManager` 를 직접 사용하여 코드 내에서 트랜잭션을 관리**하는 방법이다. 이 방법은 **트랜잭션 경계를 더 세밀하게 제어**할 수 있지만, 코드의 복잡성이 증가할 수 있다.

#### Example

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.TransactionDefinition;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.DefaultTransactionDefinition;

@Service
public class UserService {

    @Autowired
    private PlatformTransactionManager transactionManager;

    public void createUser(User user) {
        TransactionDefinition def = new DefaultTransactionDefinition();
        TransactionStatus status = transactionManager.getTransaction(def);

        try {
            // 사용자 정보 저장 로직
            userRepository.save(user);
            
            // 사용자 역할 저장 로직
            userRoleRepository.save(user.getRole());

            // 트랜잭션 커밋
            transactionManager.commit(status);
        } catch (Exception e) {
            // 트랜잭션 롤백
            transactionManager.rollback(status);
            throw e;
        }
    }
}
```

위 예제에서는 `PlatformTransactionManager` 를 사용하여 트랜잭션을 수동으로 시작하고, 작업이 완료되면 커밋하거나 예외가 발생하면 롤백하는 형식으로 트랜잭션을 관리한다.

## Isolation Levels

트랜잭션 격리 수준은 **동시에 여러 트랜잭션이 수행될 때 각 트랜잭션이 서로에게 영향을 미치는 정도를 정의**한다. 스프링은 다음과 같은 트랜잭션 격리 수준을 제공한다.

- **DEFAULT**: 기본 데이터베이스의 격리 수준을 사용한다.
- **READ_UNCOMMITTED**: 다른 트랜잭션에서 커밋되지 않은 데이터까지 읽을 수 있다. 가장 낮은 격리 수준으로, Dirty Read, Non-repeatable Read, Phantom Read가 발생할 수 있다.
- **READ_COMMITTED**: 다른 트랜잭션에서 커밋된 데이터만 읽을 수 있다. Dirty Read는 방지할 수 있으나, Non-repeatable Read, Phantom Read는 발생할 수 있다.
- **REPEATABLE_READ**: 트랜잭션 내에서 동일한 데이터를 여러 번 읽어도 동일한 결과를 보장한다. Non-repeatable Read는 방지하지만, Phantom Read는 발생할 수 있다.
- **SERIALIZABLE**: 가장 높은 격리 수준으로, 모든 트랜잭션을 순차적으로 실행하는 것과 같은 효과를 낸다. 모든 종류의 이상 현상(Dirty Read, Non-repeatable Read, Phantom Read)을 방지하지만, 성능이 저하될 수 있다.