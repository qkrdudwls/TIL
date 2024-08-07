# Introduction to the Spring IoC Container and Beans
**의존성 주입**은 IoC의 한 형태로, 두 객체 간의 관계를 맺어주는 것을 의미한다. 
의존성 주입은 인터페이스를 사이에 둬서 클래스 레벨에서는 의존관계가 고정되지 않도록 하고 **런타임 시에 관계를 동적으로 주입**하여 유연성을 확보하고 결합도를 낮출 수 있게 해준다.
IoC 컨테이너는 빈을 생성할 때 의존성을 주입하는데, 이 과정은 기본적으로 객체 자체가 클래스의 직접적인 생성 또는 Service Locator 패턴과 같은 메커니즘을 사용하여 의존성을 제어하는 것과 반대로 **제어의 역전**이라 불린다.    
org.springframework.beans 및 org.springframework.context 패키지는 스프링 프레임워크의 IoC 컨테이너의 기초가 된다. **BeanFactory** 인터페이스는 모든 유형의 객체를 관리할 수 있는 고급 구성 메커니즘을 제공한다. **ApplicationContext**는 BeanFactory의 하위 인터페이스이다. 이 인터페이스는 다음과 같은 기능을 추가한다.
* 스프링의 AOP 기능과의 더 쉬운 통합
* 국제화에 사용할 수 있는 메시지 리소스 처리
* 이벤트 발행
* 웹 애플리케이션에서 사용할 수 있는 WebApplicationContext와 같은 애플리케이션 계층 별 특정 컨텍스트
스프링 IoC 컨테이너는 스프링 빈과 빈의 생명 주기를 관리한다.
1. **Bean Factory**: Basic Spring Container
2. **Application Context**: Advanced Spring Container with enterprise-specific features
