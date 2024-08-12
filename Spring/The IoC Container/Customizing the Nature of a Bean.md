# Customizing the Nature of a Bean
## Lifecycle Callbacks

스프링은 빈의 초기화와 소멸 과정을 관리하는 데 있어 다양한 생명 주기 콜백을 지원한다. 대표적인 방법으로는 **`InitializingBean`과 `DisposableBean` 인터페이스를 구현**하는 것이 있다. 이 인터페이스들은 각각 빈의 초기화 작업과 소멸 작업을 수행할 수 있도록 콜백 메서드를 제공한다.

하지만, 스프링은 Java 표준인 JSR-250 어노테이션(`@PostConstruct`와 `@PreDestroy`)을 사용하는 것을 더 권장한다. 이 어노테이션을 사용하면, 스프링 프레임워크에 종속되지 않고도 빈의 초기화 및 소멸 콜백을 설정할 수 있기 때문이다.

또한, 스프링 빈의 생명 주기를 더욱 유연하게 관리하기 위해 **`init-method`와 `destroy-method` 속성을 사용**하거나, **`BeanPostProcessor` 인터페이스를 직접 구현**하여 빈의 초기화 및 소멸 시점을 제어할 수 있다.

### Initialization Callbacks

초기화 콜백은 스프링 컨테이너가 빈의 모든 속성을 설정한 후에 추가적인 초기화 작업을 수행하기 위해 사용된다. 이를 위해 `org.springframework.beans.factory.InitializingBean` 인터페이스를 구현할 수 있다. 이 인터페이스에는 `afterPropertiesSet()`라는 단일 메서드가 포함되어 있으며, 스프링 컨테이너가 빈의 초기화 작업을 완료한 후 이 메서드를 호출한다.

하지만, 이 방법은 스프링 프레임워크에 종속되기 때문에 일반적으로 권장되지 않는다. 대신, **`@PostConstruct` 어노테이션을 사용**하거나 XML이나 Java 기반 설정에서 **초기화 메서드를 직접 지정**하는 방법이 더 많이 사용된다.

예를 들어, XML 기반 설정에서는 `init-method` 속성을 사용하여 특정 메서드를 초기화 메서드로 지정할 수 있다. Java 기반 설정에서는 `@Bean` 어노테이션의 `initMethod` 속성을 사용할 수 있다. 이 방법들은 코드가 스프링 프레임워크에 결합되지 않아 더 유연하게 사용할 수 있다.

초기화 메서드는 빈의 구성 상태를 검증하고 필요하다면 초기화 작업을 수행하는 데 사용된다. 스프링 컨테이너는 빈의 초기화가 완료된 후에야 해당 빈을 다른 곳에서 사용할 준비가 되었음을 의미한다.

### Destruction Callbacks

소멸 콜백은 스프링 컨테이너가 **빈을 소멸시키기 전에 리소스를 정리하거나 필요한 작업을 수행**할 수 있도록 한다. 이를 위해 `org.springframework.beans.factory.DisposableBean` 인터페이스를 구현할 수 있으며, 이 인터페이스는 `destroy()`라는 단일 메서드를 포함하고 있다.

마찬가지로, `DisposableBean` 인터페이스를 구현하는 방법도 스프링에 종속되기 때문에, **`@PreDestroy` 어노테이션을 사용**하거나 XML 및 Java 설정에서 **소멸 메서드를 지정**하는 방법이 더 권장된다.

XML 설정에서는 `destroy-method` 속성을 사용하여 소멸 시 호출할 메서드를 지정할 수 있으며, Java 설정에서는 `@Bean` 어노테이션의 `destroyMethod` 속성을 사용할 수 있다. 스프링은 또한 빈 클래스에 `close` 또는 `shutdown`이라는 `public` 메서드가 있으면 이를 자동으로 감지하고 소멸 콜백으로 사용할 수 있다.

스프링의 소멸 콜백은 애플리케이션이 종료될 때 빈이 사용한 리소스를 안전하게 해제할 수 있도록 도와준다.

### Default Initialization and Destroy Methods

스프링 전용 인터페이스를 사용하지 않고도 빈의 초기화 및 소멸 콜백 메서드를 지정할 수 있다. 일반적으로 **`init()`나 `cleanup()`과 같은 메서드를 빈 클래스에 추가하여 초기화 및 소멸 작업을 수행**할 수 있다. 스프링은 빈 정의에서 `init-method`와 `destroy-method` 속성을 통해 이러한 메서드를 자동으로 호출할 수 있다.

프로젝트 전반에 걸쳐 일관된 초기화 및 소멸 메서드 이름을 사용하는 것이 좋으며, 이를 통해 개발자는 빈 정의에서 해당 메서드 이름을 별도로 지정할 필요 없이 일관된 방식으로 초기화 및 소멸 콜백을 구현할 수 있다.

이 방식은 스프링 컨테이너를 통해 애플리케이션에서 일관된 초기화 및 소멸 작업을 보장할 수 있게 한다.

### Combining Lifecycle Mechanisms

동일한 빈에 대해 서로 다른 초기화 메커니즘을 사용한 경우, 아래의 순서대로 호출된다.

1. `@PostConstruct` 어노테이션이 사용된 메서드
2. `InitializingBean` 콜백 인터페이스대로 정의된 `afterPropertiesSet()` 
3. 사용자 정의 `init()` 메서드

소멸 메서드도 동일한 순서로 호출된다.

1. `@PreDestroy` 어노테이션이 사용된 메서드
2. `DisposableBean` 콜백 인터페이스대로 정의된 `destroy()` 
3. 사용자 정의 `destroy()` 메서드

### Startup and Shutdown Callbacks

`Lifecycle` 인터페이스는 **스프링에서 관리되는 객체가 자신의 생명 주기를 관리하는데 필요한 메서드들을 정의**한다. 이 인터페이스는 다음과 같은 메서드를 포함한다.

- `void start();`: 객체의 시작 작업을 수행한다.
- `void stop();`: 객체의 종료 작업을 수행힌다.
- `boolean isRunning();`: 객체가 현재 실행 중인지 여부를 반환한다.

스프링 애플리케이션 컨텍스트는 이 인터페이스를 구현한 빈들에게 시작 및 종료 신호를 전파한다. 예를 들어, 애플리케이션 컨텍스트가 시작될 때 `start()`가 호출되고, 애플리케이션 컨텍스트가 종료될 때 `stop()`이 호출된다.

`LifecycleProcessor`는 `Lifecycle` 인터페이스를 확장하며 추가적으로 다음 두 가지 메서드를 정의한다.

- `void onRefresh();`: 애플리케이션 컨텍스트가 새로 고쳐질 때 호출된다.
- `void onClose();`: 애플리케이션 컨텍스트가 닫힐 때 호출된다.

`LifecycleProcessor`는 `Lifecycle`을 상속받기 때문에 **`start()`, `stop()`, `isRunning()` 메서드도 구현**해야 한다. `onRefresh()`와 `onClose()`는 애플리케이션 컨텍스트가 새로 고쳐지거나 닫힐 때 발생하는 콜백이다.

`SmartLifecycle` 인터페이스는 `Lifecycle`과 `Phased`를 확장하며, 더 세밀한 제어를 가능하게 한다. 주요 메서드는 다음과 같다.

- `boolean isAutoStartup();`: 객체가 자동으로 시작될 것인지 여부를 결정한다.
- `void stop(Runnable callback);`: 종료 후 호출할 콜백을 지정한다.

`SmartLifecycle` 인터페이스는 `getPhase()` 메서드를 통해 빈의 시작 및 종료 순서를 제어할 수 있다. 단계 값이 낮을수록 먼저 시작되고, 높은 값일수록 나중에 시작된다.

### Shutting Down the Spring IoC Container Gracefully in Non-Web Applications

Non-web 애플리케이션에서 스프링 IoC 컨테이너를 사용할 때는 **JVM에 종료 훅을 등록하여 종료를 보장**할 수 있다. 이렇게 하면 애플리케이션 종료 시 스프링 컨텍스트가 적절히 정리된다.

종료 훅 등록 방법은 아래와 같다. 

- `ConfigurableApplicationContext` 인터페이스의 `registerShutdownHook()` 메서드를 호출한다.

아래는 종료 훅을 등록하는 예제이다.

```java
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public final class Boot {

	public static void main(final String[] args) throws Exception {
		ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");

		// 종료 훅을 추가한다.
		ctx.registerShutdownHook();

		// 애플리케이션이 실행되는 곳
	}
}
```

`registerShutdownHook()` 메소드는 **JVM 종료 시 스프링 컨텍스트의 `destroy` 메서드를 호출하여 리소스를 정리**한다.

### Thread Safety and Visibility

스프링 코어 컨테이너는 싱글톤 인스턴스를 스레드 안전하게 처리하며, 다음과 같은 보장 사항이 있다.

- **스레드 안전성**: 스프링은 싱글톤 인스턴스에 대해 스레드 안전한 접근을 보장합니다. 이는 내부적으로 싱글톤 잠금을 사용하여 처리된다.
- **가시성 보장**: 빈의 초기화 상태는 스레드 안전하며, 일반 구성 필드는 초기화 단계 동안만 변경되는 경우 `volatile`로 선언할 필요가 없다.

빈 인스턴스가 스레드 안전하게 접근되며, 런타임 상태가 누적되는 경우 스레드 안전한 구조나 `volatile` 필드를 사용해야 한다.

## `ApplicationContextAware` and `BeanNameAware`

스프링에서 `ApplicationContext`가 `ApplicationContextAware` 인터페이스를 구현한 빈을 생성하면, 해당 빈은 `ApplicationContext`에 대한 참조를 받는다. 이 참조를 통해 빈은 애플리케이션 컨텍스트에 직접 접근할 수 있다. 다음은 `ApplicationContextAware` 인터페이스의 정의이다.

```java
public interface ApplicationContextAware {

	void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```

`BeanNameAware` 인터페이스를 구현하면 빈은 자신의 이름을 받을 수 있다. 이 정보는 빈이 생성된 후, 다른 초기화 작업이 수행되기 전에 제공된다. 다음은 `BeanNameAware` 인터페이스의 정의이다.

```java
public interface BeanNameAware {

	void setBeanName(String name) throws BeansException;
}
```

이러한 인터페이스들은 빈이 특정 인프라 종속성에 접근할 수 있도록 하며, 일반적으로 인프라 빈이 필요로 하는 기능을 제공한다. 이러한 방법을 사용하면 코드가 스프링 API에 결합되며, 일반적인 Inversion of Control 스타일을 따르지 않으므로, 일반적으로 인프라 빈에만 사용하는 것이 좋다.