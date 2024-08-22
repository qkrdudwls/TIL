# DAO, DTO, VO, and Entity

DAO, DTO, VO, 그리고 Entity는 객체 지향 프로그래밍과 데이터베이스와의 상호 작용에서 중요한 역할을 한다. 

## DAO

DAO(Data Access Object)는 **데이터베이스와의 상호 작용을 담당하는 객체**이다. 주로 데이터베이스에 대한 **CRUD 작업을 처리**하며, 애플리케이션의 다른 부분에서 데이터베이스와 직접적으로 상호 작용하지 않도록 해준다. 즉, **데이터베이스 접근 로직을 캡슐화**하여 애플리케이션의 **비즈니스 로직과 분리**한다.

### Features

- **데이터베이스 독립성**: DAO는 데이터베이스 접근 로직을 캡슐화하므로, 데이터베이스 구조나 접근 방법이 변경되더라도 DAO 내부에서만 수정하면 된다.
- **재사용성**: 동일한 DAO를 여러 곳에서 재사용할 수 있다.
- **테스트 용이성**: DAO 패턴을 사용하면 데이터베이스와의 상호 작용을 모의 객체(mock object)로 대체해 단위 테스트를 쉽게 수행할 수 있다.

### Example

```java
public class UserDao {

    private JdbcTemplate jdbcTemplate;

    public UserDao(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }

    public User getUserById(int id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new Object[]{id}, new UserRowMapper());
    }

    public void insertUser(User user) {
        String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
        jdbcTemplate.update(sql, user.getName(), user.getEmail());
    }

    // etc
}
```

## DTO

DTO(Data Transfer Object)는 **계층 간 데이터 전송을 위한 객체**이다. 주로 클라이언트와 서버 간, 혹은 서로 다른 시스템 간에 데이터를 주고받을 때 사용된다. DTO는 보통 **순수 데이터만을 포함**하며, 로직은 포함하지 않는다. **데이터 전송의 효율성을 높이고, 특정 계층의 데이터 구조를 다른 계층에 노출하지 않도록 하는 데 목적**이 있다.

### Features

- **직렬화 기능**: 네트워크 전송이나 파일 저장을 위해 직렬화할 수 있어야 한다.
- **비즈니스 로직 X**: DTO는 데이터를 담는 그릇 역할만 하며, 비즈니스 로직을 포함하지 않는다.
- **경량 객체**: 가능한 한 가볍게 설계되어야 하며, 필요한 최소한의 데이터만 포함한다.

### Example

```java
public class UserDTO {
    private int id;
    private String name;
    private String email;

    // 생성자, getter, setter 메서드들
}
```

## VO

VO(Value Object)는 **변하지 않는 값**을 가지며, **동일한 데이터를 가지고 있다면 동일한 객체로 간주**되는 객체로, 주로 값의 묶음을 표현하는 데 사용된다. **`equals()` 와 `hashCode()` 메서드를 오버라이드**하여 동일한 값을 가진 VO 객체들이 동일한 것으로 인식되도록 설계한다. **불변성을 유지하는 것이 중요**한 특징이다.

### Features

- **불변성**: VO 객체는 생성된 이후 그 상태를 변경할 수 없다. 불변성을 유지함으로써 여러 스레드에서 안전하게 사용할 수 있다.
- **동등성**: 같은 값을 가지는 두 VO 객체는 동일한 것으로 간주된다.
- **작은 객체**: VO는 주로 단일 값을 나타내거나 몇 개의 값을 묶어 표현한다.

### Example

```java
public class Money {
    private final int amount;
    private final String currency;

    public Money(int amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    public int getAmount() {
        return amount;
    }

    public String getCurrency() {
        return currency;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Money money = (Money) o;
        return amount == money.amount && Objects.equals(currency, money.currency);
    }

    @Override
    public int hashCode() {
        return Objects.hash(amount, currency);
    }
}
```

## Entity

엔티티는 **데이터베이스 테이블과 1:1 매핑되는 객체**이다. 주로 JPA와 같은 ORM 프레임워크에서 사용된다. 데이터베이스의 행 하나를 엔티티 객체 하나가 표현하며, 엔티티는 **데이터베이스와의 동기화 상태를 유지**한다.

### Features

- **영속성**: 엔티티 객체는 데이터베이스에 저장되거나 데이터베이스에서 조회된 데이터를 나타낸다.
- **식별자**: 각 엔티티 객체는 고유 식별자를 가지고 있다.
- **비즈니스 로직**: 엔티티는 비즈니스 로직을 포함할 수 있지만, 복잡한 로직은 서비스 계층으로 분리하는 것이 좋다.

### Example

```java
@Entity
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // getter, setter, 비즈니스 메서드들
}
```
