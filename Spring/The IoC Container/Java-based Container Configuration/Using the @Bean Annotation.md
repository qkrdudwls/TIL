# Using the `@Bean` Annotation

`@Bean` 어노테이션은 스프링 프레임워크에서 자바 객체를 관리하는 **스프링 컨테이너에서 관리하는 빈으로 등록하기 위해 사용**된다. `@Bean` 어노테이션은 주로 **`@Configuration` 어노테이션이 붙은 클래스 내에서 사용**된다.

## Concept

- **빈 등록**: `@Bean` 어노테이션은 메서드에 붙여서 그 메서드가 반환하는 객체를 스프링 컨테이너에 빈으로 등록한다.
- **메서드 수준에서 사용**: `@Bean` 어노테이션은 메서드 위에 사용되며, 해당 메서드가 반환하는 객체가 스프링 컨테이너에서 관리된다.
- **사용처**: 일반적으로 외부 라이브러리의 클래스를 빈으로 등록하거나, 복잡한 빈 설정이 필요한 경우 사용된다.

## Relationship with `@Configuration`

- `@Bean` 어노테이션은 보통 `@Configuration` 어노테이션이 붙은 클래스 내에서 사용된다.
- `@Configuration` 어노테이션은 해당 클래스가 하나 이상의 `@Bean` 메서드를 포함하고 있으며, 스프링 컨테이너에서 이 클래스의 인스턴스를 사용하여 빈을 생성하고 관리할 것임을 명시한다.

## `@Bean` Method

- `@Bean` 메서드는 인자를 가질 수 있으며, 스프링은 이 메서드를 호출할 때 해당 인자에 필요한 빈을 자동으로 주입한다.
- 예를 들어, 다음과 같이 정의할 수 있다.
    
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
    
    위 예제에서 `myService` 메서드는 `MyServiceImpl` 객체를 빈으로 등록하며, `myController` 메서드는 `MyService` 타입의 빈을 주입받아 `MyController` 객체를 생성한다.
    

### 빈의 이름 지정

- `@Bean` 어노테이션은 기본적으로 메서드 이름을 빈 이름으로 사용한다. 하지만, 빈의 이름을 명시적으로 지정할 수도 있다.
    
    ```java
    @Bean(name = "customService")
    public MyService myService() {
        return new MyServiceImpl();
    }
    ```
    

### 생명 주기 메서드 지정

- `@Bean` 어노테이션을 사용할 때, 빈의 초기화(`initMethod`)와 소멸(`destroyMethod`) 메서드를 지정할 수 있다.
    
    ```java
    @Bean(initMethod = "init", destroyMethod = "cleanup")
    public MyService myService() {
        return new MyServiceImpl();
    }
    ```
    
- 위 예제에서 `myService` 메서드가 생성한 빈은 초기화 시 `init()` 메서드를, 소멸 시 `cleanup()` 메서드를 호출한다.

### 조건부 빈 등록

- `@Conditional` 어노테이션을 사용하여 특정 조건이 만족될 때만 빈을 등록하도록 설정할 수 있다.

## Advantages

- **유연성**: XML 설정 파일 대신 자바 코드로 빈을 구성할 수 있어, 코드 기반의 유연한 설정이 가능하다.
- **타입 안전성**: XML 설정과 달리, 컴파일 시점에 타입을 체크할 수 있다.
- **외부 라이브러리 지원**: 외부 라이브러리의 객체를 스프링 컨테이너에서 관리하는 빈으로 등록하기 쉽다.