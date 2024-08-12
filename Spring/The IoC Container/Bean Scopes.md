# Bean Scopes
스프링 프레임워크에서 빈 스코프에 대한 개념은 **스프링 IoC 컨테이너 내에서 빈의 생명 주기와 가시성을 관리**하는 데 핵심적이다. 다양한 스코프를 통해 **빈 인스턴스가 생성되는 방법과 시기, 그리고 생명 주기 전반에 걸쳐 빈 인스턴스가 작동하는 방식을 제어**할 수 있다.  

## The Singleton Scope

이 스코프는 **스프링 IoC 컨테이너 당 빈 인스턴스 하나만 생성**되도록 한다. 빈에 대한 **모든 요청은 동일한 인스턴스를 반환**한다.

스프링의 싱글톤 빈 개념은 GoF(Gang of Four) 패턴 책에서 정의된 싱글톤 패턴과 다르다. GoF 싱글톤은 객체의 스코프를 고정시켜, 특정 클래스의 인스턴스를 ClassLoader당 하나만 생성하도록 하지만 스프링의 싱글톤 스코프는 컨테이너별, 빈별로 정의되며, 동일한 클래스에 대해 여러 빈 정의가 있는 경우 각 컨테이너마다 하나의 인스턴스가 생성된다. **스프링에서 싱글톤 스코프는 기본값으로 설정**된다.

싱글톤 스코프는 애플리케이션에서 **단일 인스턴스를 공유해야 하는 stateless 빈이나 컴포넌트에 이상적**이다.

```xml
<bean id="accountService" class="com.example.AccountService" scope="singleton"/>
```

## The Prototype Scope

프로토타입 스코프를 사용하면 해**당 빈에 대한 요청이 이루어질 때마다 빈의 새 인스턴스가 생성**된다. 즉, 빈이 다른 빈에 주입되거나 `getBean()` 메서드를 통해 요청될 때마다 새로운 빈 인스턴스가 생성된다. 스프링은 prototype 빈을 초기화하고 구성만 하기 때문에 클라이언트는 전체 생명 주기를 관리해야 한다.

프로토타입 스코프는 **각 요청이 다른 인스턴스에서 작동해야 하는 stateful 빈에 적합**하다. 

```xml
<bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
```

## Singleton Beans with Prototype-bean Dependencies

프로토타입 빈에 대한 의존성이 있는 싱글톤 스코프 빈을 사용하는 경우, 의존성이 인스턴스화 시점에 해결된다는 점을 유의해야 한다. 프로토타입 스코프 빈을 싱글톤 스코프 빈에 의존성을 주입하는 경우, 새 프로토타입 빈이 인스턴스화 되고 싱글톤 빈에 의존성이 주입된다. 이때, **프로토타입 인스턴스는 싱글톤 스코프 범위에 제공되는 유일한 인스턴스**이다.

만약 싱글톤 스코프 빈이 런타임에 프로토타입 스코프 빈의 새 인스턴스를 반복적으로 획득하기를 원한다면, 의존성은 한 번만 주입하기 때문에 프로토타입 스코프 빈을 싱글톤 빈에 의존성 주입을 할 수 없다. 런타임 시 두 번 이상 프로토타입 스코프 빈의 새 인스턴스가 필요할 때는 **메소드 주입**으로 가능하다.

## Request, Session, Application, and WebSocket Scopes

`request` , `session` , `application` , 그리고 `websocket` 스코프는 **web-aware 스프링 ApplicationContext에서만 사용**할 수 있다. 일반적인 스프링 IoC 컨테이너에서 이 스코프를 사용하려고 하면 `IllegalStateException`이 발생한다.

### Request Scope

요청 스코프는 **하나의 HTTP 요청의 생명 주기에 빈 정의를 스코프**한다. 각 HTTP 요청은 하나의 빈 인스턴스를 갖는다.

요청 스코프는 요청 범위 데이터나 양식과 같이 **각 요청에 빈의 고유한 인스턴스가 있어야 하는 웹 애플리케이션에 적합**하다. 

```xml
<bean id="loginAction" class="com.example.LoginAction" scope="request"/>
```

어노테이션을 사용하는 경우는 아래와 같다.

```java
@RequestScope
@Component
public class LoginAction { ... }
```

### Session Scope

세션 스코프는 **하나의 HTTP 세션의 생명 주기에 빈 정의를 스코프**한다. HTTP 세션마다 새 빈 인스턴스가 생성되며, 세션 기간 동안 지속된다.

세션 스코프는 **사용자 세션 별 데이터를 유지하는 데 유용**하다.

```xml
<bean id="userPreferences" class="com.example.UserPreferences" scope="session"/>
```

어노테이션을 사용하는 경우는 아래와 같다.

```java
@SessionScope
@Component
public class UserPreferences { ... }
```

### Application Scope

애플리케이션 스코프를 사용하면 **빈 인스턴스는 ServletContext의 생명 주기로 스코프가 지정**된다. 사실상 웹 애플리케이션 컨택스트 내의 싱글톤이다.

애플리케이션 스코프는 전체 애플리케이션에서 사용할 수 있어야 하는 **공유 리소스나 configuration 데이터에 적합**하다.

```xml
<bean id="appPreferences" class="com.example.AppPreferences" scope="application"/>
```

어노테이션을 사용하는 경우는 아래와 같다.

```java
@ApplicationScope
@Component
public class AppPreferences { ... }
```

### WebSocket Scope

웹소켓 스코프를 사용하면 빈 인스턴스는 **웹소켓 세션의 생명 주기로 스코프가 지정**되며, 일반적으로 STOMP over WebSocket 애플리케이션에서 사용된다. 이때 **웹소켓과 함께 @Scope 어노테이션을 함께 사용**한다.

웹소켓 스코프는 **웹소켓 연결 기간 동안 존재해야 하는 빈에 적합**하다.

## Custom Scopes

스프링에서는 사용자 정의 스코프를 생성하고 사용할 수 있다. 예를 들어, `SimpleThreadScope`와 같은 스레드 스코프를 사용할 수 있다. 사용자 정의 스코프를 생성하려면 **`Scope` 인터페이스를 구현하고, 이를 스프링 컨테이너에 등록**해야 한다. `ConfigurableBeanFactory` 인터페이스를 통해 이 작업을 수행할 수 있으며, `CustomScopeConfigurer` 클래스를 사용해 스코프를 등록할 수도 있다.

예를 들어, 다음과 같이 스레드 스코프를 등록할 수 있다.

```java
Scope threadScope = new SimpleThreadScope();
beanFactory.registerScope("thread", threadScope);
```

이렇게 등록된 스코프를 빈 정의에서 사용할 수 있다.

```xml
<bean id="..." class="..." scope="thread"/>
```

스프링에서 사용자 정의 스코프를 활용해 유연하게 빈을 관리할 수 있으며, 이를 통해 애플리케이션의 요구 사항에 맞는 빈 생명 주기를 구현할 수 있다.