# Bean
## Spring Bean
스프링 빈은 스프링 프레임워크에서 관리하는 **객체**를 의미한다. 빈은 스프링 IoC 컨테이너에 의해 인스턴스화되고, 관리되고, 구성되는 객체로, 애플리케이션 내에서 주로 **서비스**, **데이터 액세스 개체 (DAO)**, **컨트롤러** 등과 같은 컴포넌트를 나타낸다. 빈은 주로 **XML 설정 파일, 자바 설정 클래스, 그리고 어노테이션으로 정의**할 수 있다.
## Dependency Injection
스프링 빈 간의 의존성은 주로 생성자 주입, 세터 주입, 그리고 필드 주입을 통해 설정된다.
1. **생성자 주입**: 생성자를 통해 의존성을 주입한다.
    ``` java
    @Component
    public class MyBean {
    
        private final Dependency dependency;

        @Autowired
        public MyBean(Dependency dependency) {
            this.dependency = dependency;
        }
    }
    ```
2. **세터 주입**: 세터 메서드를 통해 의존성을 주입한다.
   ``` java
   @Component
    public class MyBean {
    
        private Dependency dependency;

        @Autowired
        public void setDependency(Dependency dependency) {
            this.dependency = dependency;
        }
    }
    ```
3. **필드 주입**: 필드에 직접 의존성을 주입한다.
   ``` java
   @Component
    public class MyBean {
    
        @Autowired
        private Dependency dependency;
    }
    ```
## Scope
빈 스코프는 스프링 빈이 생성되고 사용되는 범위를 정의한다. 스프링 프레임워크는 다양한 스코프를 제공하여 빈의 생명 주기를 제어하고, 애플리케이션의 요구에 맞게 빈을 관리할 수 있도록 한다. 각 스코프는 빈의 인스턴스가 생성되고 유지되는 방식을 다르게 정의한다.
1. **Singleton Scope**
* **역할**: 스프링 컨테이너에서 기본적으로 설정되는 스코프로, 애플리케이션 컨텍스트 당 하나의 빈 인스턴스만 생성된다.
* **특징**
  * 빈이 최초로 요청될 때 생성되고, 이후에는 동일한 인스턴스를 반환한다.
  * 상태를 공유하는 빈을 필요로 할 때 유용하다.
* **예시**
  ``` java
  @Bean
  @Scope("singleton")
  public MyBean myBean(){
    return new MyBean();
  }
  ```
2. **Prototype Scope**
- **역할**: 요청할 때마다 새로운 빈 인스턴스가 생성된다.
- **특징**
    - 각 요청마다 빈의 새로운 인스턴스가 필요할 때 사용한다.
    - 상태를 공유하지 않고 독립적인 빈 인스턴스를 필요로 할 때 유용하다.
- **예시**
    
    ```java
    @Bean
    @Scope("prototype")
    public MyBean myBean() {
        return new MyBean();
    }
    ```
    
3. **Request Scope**
- **역할**: 각 HTTP 요청마다 새로운 빈 인스턴스가 생성된다.
- **특징**
    - 웹 애플리케이션에서 사용된다.
    - HTTP 요청의 lifecycle 동안에만 빈이 유지된다.
- **예시**
    
    ```java
    @Bean
    @Scope("request")
    public MyBean myBean() {
        return new MyBean();
    }
    ```
    
4. **Session Scope**
- **역할**: 각 HTTP 세션마다 새로운 빈 인스턴스가 생성된다.
- **특징**
    - 웹 애플리케이션에서 사용된다.
    - 세션의 lifecycle 동안에 빈이 유지된다.
- **예시**
    
    ```java
    @Bean
    @Scope("session")
    public MyBean myBean() {
        return new MyBean();
    }
    ```
    
5. **Application Scope**
- **역할**: SevletContext당 하나의 빈 인스턴스가 생성된다.
- **특징**
    - 웹 애플리케이션에서 사용된다.
    - 애플리케이션 전반에 걸쳐 빈을 공유할 때 사용된다.
- **예시**
    
    ```java
    @Bean
    @Scope("application")
    public MyBean myBean() {
        return new MyBean();
    }
    ```
    
6. **WebSocket Scope**
- **역할**: 각 WebSocket 세션마다 새로운 빈 인스턴스가 생성된다.
- **특징**
    - WebSocket 통신의 lifecycle 동안에 빈이 유지된다.
- **예시**
    
    ```java
    @Bean
    @Scope("websocket")
    public MyBean myBean() {
        return new MyBean();
    }
    ```
    

## Bean Lifecycle

스프링은 빈의 생명 주기를 관리한다. 이는 빈의 초기화와 소멸 작업을 포함한다. 

- **초기화 메서드**: 빈이 생성된 후 호출된다.
    
    ```java
    @Bean(initMethod = "init")
    public MyBean myBean() {
        return new MyBean();
    }
    
    public class MyBean {
        public void init() {
            // 초기화 작업
        }
    }
    ```
    
- **소멸 메서드**: 빈이 소멸되기 전에 호출된다.
    
    ```java
    @Bean(destroyMethod = "destroy")
    public MyBean myBean() {
        return new MyBean();
    }
    
    public class MyBean {
        public void destroy() {
            // 소멸 작업
        }
    }
    ```
    
- **@PostConstruct와 @PreDestroy 어노테이션**: Java 표준 어노테이션을 사용하여 초기화 및 소멸 메서드를 지정할 수 있다.
    
    ```java
    @Component
    public class MyBean {
        
        @PostConstruct
        public void init() {
            // 초기화 작업
        }
    
        @PreDestroy
        public void destroy() {
            // 소멸 작업
        }
    }
    ```