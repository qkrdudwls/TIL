# Container
## Spring Container
스프링 컨테이너는 스프링 프레임워크의 핵심 구성 요소로, 스프링 애플리케이션 객체(빈)들을 관리하는 '컨테이너'이다. 스프링 컨테이너는 애플리케이션 생성 시 **객체를 생성**하고, 설정 파일 또는 어노테이션을 기반으로 객체 간의 **의존성을 주입**한다.
## Inversion of Control (IoC) and Dependency Injection (DI)
스프링 컨테이너의 핵심 개념은 **IoC**와 **DI**이다.
* **IoC**: 전통적인 프로그래밍 방식에서는 객체가 스스로 다른 객체를 생성하거나 찾지만, IoC를 사용하면 객체의 생성 및 의존성 관리를 컨테이너가 담당하게 된다. 객체는 필요한 의존성을 직접 생성하지 않고, 컨테이너로부터 주입받는다.
* **DI**: DI는 객체가 필요한 의존성을 외부에서 주입받는 방식이다. 스프링 컨테이너는 설정 파일이나 어노테이션을 통해 객체 간의 의존성을 정의하고, 이를 자동으로 주입한다.
## Types of Spring Containers
1. **BeanFactory**: 가장 기본적인 컨테이너로, 객체의 생성과 의존성 주입을 담당한다. 초기화 비용이 적고, 지연 로딩(lazy loading)을 지원한다.
2. **ApplicationContext**: BeanFactory를 확장한 컨테이너로, 더 많은 기능을 제공한다. 메시지 소스 처리, 이벤트 발행, 애플리케이션 레벨 설정 등을 지원한다. 주요 구현체로는 ClassPathXmlApplicationContext, FileSystemXmlApplicationContext, AnnotationConfigApplicationContext 등이 있다.
## Spring Container Lifecycle
스프링 컨테이너는 애플리케이션의 시작부터 종료까지 다양한 단계에서 생명 주기 **콜백**을 제공한다. 대표적인 생명 주기 콜백 메서드로는 InitializingBean의 afterPropertiesSet과 DisposableBean의 destroy가 있으며, @PostConstruct와 @PreDestroy 어노테이션을 통해 콜백을 정의할 수 있다.
## Configuration Metadata
Spring Configuration Metadata는 스프링 애플리케이션의 객체 설정과 구성 정보를 정의하는 방법을 의미한다. 이는 **XML 파일**, **자바 어노테이션**, 또는 **자바 설정 클래스**를 통해 이루어진다. 각각의 방법은 애플리케이션의 다양한 구성 요소와 의존성을 선언하고 설정하는 데 사용된다. 
### Annotation
스프링은 여러가지 어노테이션을 통해 빈 구성 및 의존성 주입을 지원한다.
* **@Component**: 클래스를 스캔하여 자동으로 빈으로 등록한다.
* **@Autowired**: 의존성을 자동으로 주입한다.
* **@Qualifier**: 같은 타입의 빈이 여러 개 있을 때 특정 빈을 선택할 수 있도록 한다.
* **@Configuration**: 설정 클래스를 정의한다.
* **@Bean**: 메서드에 적용하여 해당 메서드의 반환값을 빈으로 등록한다.
