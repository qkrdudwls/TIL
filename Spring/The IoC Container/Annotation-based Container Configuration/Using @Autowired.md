# Using `@Autowired`

`@Autowired` 어노테이션은 스프링 프레임워크에서 의존성 주입을 간편하게 처리할 수 있도록 도와주는 어노테이션이다. 스프링에서는 애플리케이션 컴포넌트 간의 의존성을 자동으로 연결해 주는 다양한 방법을 제공하는데, 그 중 하나가 `@Autowired`를 이용한 자동 주입(Autowiring)이다.

## Basic Concept

`@Autowired` 어노테이션은 스프링 컨테이너가 관리하는 빈(Bean) 중에서, **해당 타입에 맞는 빈을 자동으로 찾아 주입하는 기능을 제공**한다. 주로 클래스의 필드, 생성자, 또는 메서드에 사용되어, 해당 타입의 빈을 자동으로 주입할 수 있다. 
스프링은 `@Autowired`를 통해 주입될 빈을 찾을 때, 기본적으로 **타입을 기준으로 검색**한다. 동일한 타입의 빈이 하나만 존재할 경우, 해당 빈이 주입된다. 만약 동일한 타입의 빈이 여러 개 있을 경우에는 이름을 기준으로 추가적인 필터링이 이루어진다.

```java
@Component
public class MyService {

    @Autowired
    private MyRepository myRepository;

    // or

    @Autowired
    public MyService(MyRepository myRepository) {
        this.myRepository = myRepository;
    }

    // or

    @Autowired
    public void setMyRepository(MyRepository myRepository) {
        this.myRepository = myRepository;
    }
}
```

## Location of Use

`@Autowired` 어노테이션은 다음과 같은 위치에서 사용할 수 있다.

- **필드(Field):** 해당 필드에 직접 빈을 주입한다.
- **생성자(Constructor):** 생성자에서 필요한 의존성을 주입할 수 있다. 생성자 주입 방식은 특히 필드의 불변성을 유지하는 데 유리하다.
- **세터 메서드(Setter Method):** 의존성을 선택적으로 주입할 수 있다. 필수적인 의존성이 아닌 경우에 유용하다.

## `@Autowired` and Multiple Beans

만약 동일한 타입의 빈이 여러 개 존재할 경우, 스프링은 어떤 빈을 주입할지 명확하지 않아 `NoUniqueBeanDefinitionException` 예외가 발생할 수 있다. 이를 해결하기 위해 다음과 같은 방법을 사용할 수 있다.

- **@Qualifier 어노테이션 사용:** 특정 빈을 명시적으로 선택할 수 있도록 `@Qualifier` 어노테이션을 함께 사용한다.
    
    ```java
    @Autowired
    @Qualifier("specificBeanName")
    private MyRepository myRepository;
    ```
    
- **Primary 빈 설정:** `@Primary` 어노테이션을 사용하여 기본적으로 주입될 빈을 설정할 수 있다.
    
    ```java
    @Primary
    @Component
    public class PrimaryRepository implements MyRepository {
        // ...
    }
    ```
    

## Optional Injection

때로는 의존성이 필수가 아닐 수 있다. 이 경우 `@Autowired(required = false)`를 사용하여 주입할 빈이 존재하지 않을 때도 예외를 발생시키지 않도록 할 수 있다.

```java
@Autowired(required = false)
private Optional<MyRepository> myRepository;
```
