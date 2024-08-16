# Using the `@Configuration` Annotation

`@Configuration` 어노테이션은 스프링 프레임워크에서 **설정 클래스를 정의하기 위해 사용**되는 어노테이션이다. 이 어노테이션은 해당 클래스가 **하나 이상의 `@Bean` 메서드를 포함**하고 있으며, 스프링 컨테이너가 **이 클래스의 인스턴스를 빈 팩토리로 사용할 것**임을 나타낸다. `@Configuration` 어노테이션은 주로 자바 기반의 설정(Java Configuration)에서 사용되며, XML 설정을 대체하거나 보완하는 역할을 한다.

## Concept

- **설정 클래스**: `@Configuration`이 붙은 클래스는 스프링 애플리케이션의 설정 정보를 담고 있는 클래스로 간주된다. 이 클래스는 하나 이상의 `@Bean` 메서드를 포함하고 있으며, 이 메서드들이 반환하는 객체들이 스프링 컨테이너에 의해 관리되는 빈이 된다.
- **빈 팩토리**: `@Configuration` 클래스는 스프링 컨테이너에서 빈 팩토리로 사용된다. 이 클래스의 메서드들은 스프링 컨테이너에서 호출되어 빈을 생성하고, 이 빈들은 애플리케이션 내에서 다른 빈들에게 주입된다.

## Relationship with `@Bean`

- `@Configuration` 클래스는 주로 `@Bean` 어노테이션이 붙은 메서드들을 포함한다. `@Bean` 메서드는 반환된 객체를 스프링 컨테이너에 빈으로 등록한다. 이 빈들은 필요할 때 다른 빈에게 주입된다.
    
    ```java
    @Configuration
    public class AppConfig {
    
        @Bean
        public MyService myService() {
            return new MyServiceImpl();
        }
    
        @Bean
        public MyController myController() {
            return new MyController(myService());
        }
    }
    ```
    
    위 코드에서 `AppConfig` 클래스는 설정 클래스이며, `myService()`와 `myController()` 메서드는 각각 `MyService`와 `MyController` 타입의 빈을 정의한다.
    

## Dependency Management between Beans

- `@Configuration` 클래스에서 정의된 `@Bean` 메서드 간의 호출은 메서드 호출이 아닌 스프링 컨테이너에서 관리된다. 이를 통해 메서드 간 의존성을 관리할 수 있다.
    
    ```java
    @Configuration
    public class AppConfig {
    
        @Bean
        public MyService myService() {
            return new MyServiceImpl();
        }
    
        @Bean
        public MyController myController(MyService myService) {
            return new MyController(myService);
        }
    }
    ```
    
    위 예제에서 `myController()` 메서드는 `myService()` 메서드가 반환하는 빈을 의존성으로 가진다.
    

## Relationship with `@ComponentScan`

- `@Configuration` 클래스는 종종 `@ComponentScan`과 함께 사용된다. `@ComponentScan`은 지정된 패키지에서 스프링 빈으로 등록할 클래스를 자동으로 스캔한다. 이를 통해 `@Component`, `@Service`, `@Repository`, `@Controller` 등의 어노테이션이 붙은 클래스를 자동으로 스프링 컨테이너에 빈으로 등록할 수 있다.
    
    ```java
    @Configuration
    @ComponentScan(basePackages = "com.example.myapp")
    public class AppConfig {
        // 빈 정의 메서드들
    }
    ```
    
    위 예제에서 `AppConfig` 클래스는 `com.example.myapp` 패키지에서 컴포넌트를 스캔하여 빈으로 등록하고, 동시에 직접 정의한 빈도 포함할 수 있다.
    

## Advantages

- **코드 기반 설정**: XML 설정 파일 대신 자바 코드로 설정을 작성할 수 있어, 코드 기반의 설정이 더욱 직관적이고 유지보수가 용이하다.
- **타입 안전성**: 자바의 타입 시스템을 활용하여 설정 시 컴파일 타임에 타입 오류를 방지할 수 있다.
- **유연성**: 복잡한 빈 정의나 동적 빈 생성을 위해 자바 코드를 활용할 수 있다.
- **프로필 기반 설정**: `@Profile` 어노테이션과 함께 사용하여 환경에 따라 다른 설정을 적용할 수 있다.