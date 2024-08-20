# Logging

스프링에서 로깅은 **애플리케이션의 상태, 동작, 오류 등을 기록하여 개발자나 운영자가 애플리케이션의 동작을 이해하고 문제를 진단할 수 있도록 돕는 중요한 기능**이다. 스프링에서는 로깅을 쉽게 설정하고 활용할 수 있도록 다양한 로깅 프레임워크와의 통합을 지원한다.

## Importance

- **디버깅 및 문제 해결**: 애플리케이션이 예상치 못한 동작을 할 때, 로깅을 통해 원인을 파악할 수 있다.
- **모니터링**: 애플리케이션이 동작 상태를 실시간으로 모니터링하고, 성능 저하나 오류 발생 시 경고를 받을 수 있다.
- **이력 추적**: 중요한 이벤트나 트랜잭션을 기록하여 나중에 분석할 수 있다.
- **보안 감사**: 보안 관련 이벤트를 기록하여 보안 위협을 식별하고 대응할 수 있다.

## Logging Framework

스프링에서는 다양한 로깅 프레임워크를 사용할 수 있다. 가장 일반적으로 사용되는  로깅 프레임워크는 다음과 같다. 

- **SLF4J**: SLF4J (Simple Logging Facade for Java)는 로깅을 위한 추상화 계층으로, 실제 로깅을 담당하는 프레임워크에 대한 일관된 인터페이스를 제공한다. SLF4J를 사용하면 로깅 구현체를 쉽게 교체할 수 있다.
- **Logback**: Logback은 SLF4J의 기본 구현체로, 성능이 우수하고 설정이 용이하며, XML 기반의 설정 파일을 통해 강력한 기능을 제공한다. Logback은 스프링 부트에서도 기본 로깅 프레임워크로 사용된다.
- **Log4j2**: Apache Log4j2는 다양한 기능을 제공하는 로깅 프레임워크로, 성능이 우수하고 유연한 설정이 가능하다. 과거의 Log4j1.x는 보안 문제로 사용이 권장되지 않지만, Log4j2는 개선된 기능과 성능을 제공한다.
- **Java Util Logging**: Java Util Logging(JUL)은 JDK에 기본으로 포함된 로깅 프레임워크로, 간단한 로깅 기능을 제공한다. 기능이 제한적이기 때문에 대규모 애플리케이션에서는 잘 사용되지 않는다.

## Logging Level

로깅 레벨은 **로그 메시지의 중요도**를 나타내며, 필요에 따라 로깅 레벨을 설정하여 특정 레벨 이상의 로그만 출력할 수 있다. 일반적으로 사용되는 로깅 레벨은 다음과 같다.

- **TRACE**: 가장 낮은 레벨로, 매우 상세한 디버깅 정보를 기록할 때 사용된다.
- **DEBUG**: 디버깅을 위한 정보를 기록한다. 개발 환경에서 주로 사용된다.
- **INFO**: 일반적인 운영 정보를 기록한다. 애플리케이션의 주요 이벤트를 기록하는 데 사용된다.
- **WARN**: 경고성 메시지를 기록한다. 잠재적인 문제를 나타내지만, 애플리케이션이 계속 동작할 수 있는 경우 사용된다.
- **ERROR**: 오류 메시지를 기록한다. 애플리케이션이 정상적으로 동작할 수 없는 경우 사용된다.
- **OFF**: 모든 로그 출력을 비활성화한다.

로깅 레벨은 **설정된 레벨보다 높은 중요도를 가진 메시지만 출력**한다. 예를 들어, 로깅 레벨이 `INFO` 로 설정된 경우, `INFO`, `WARN`, `ERROR` 메시지만 출력되고, `DEBUG`나 `TRACE` 메시지는 출력되지 않는다.

## Logging Output Target

로그는 다양한 대상에 출력할 수 있다.

- **콘솔**: 개발 중에는 콘솔에 로그를 출력하여 실시간으로 확인할 수 있다.
- **파일**: 운영 환경에서는 로그 파일에 기록하여, 로그 데이터를 보존하고 나중에 분석할 수 있다.
- **데이터베이스**: 로그 데이터를 데이터베이스에 저장하여 중앙 집중식 관리 및 분석이 가능하다.
- **원격 서버**: 네트워크를 통해 원격 로깅 서버에 로그를 전송하여 실시간 모니터링하고 분석을 할 수 있다.

## Logger

`Logger`는 **로깅을 실제로 수행하는 객체**이다. 로깅 메시지를 기록하고, 해당 메시지를 다양한 로깅 레벨에 따라 관리한다. `Logger` 객체는 **주로 특정 클래스나 애플리케이션의 로깅을 관리하기 위해 사용**된다.

### Features

- **로깅 레벨 관리**: `Logger` 는 다양한 로깅 레벨을 제공한다. `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR` 등의 로깅 레벨을 통해 메시지를 기록하고, 로깅 설정에 따라 출력할 메시지를 결정한다.
- **메시지 포맷팅**: `Logger` 는 메시지의 포맷을 관리하고, 추가적인 정보를 함께 기록할 수 있다.
- **성능 최적화**: `Logger` 는 로깅 메시지를 필요할 때만 생성하도록 최적화할 수 있다. 불필요한 로깅이 성능에 미치는 영향을 줄이기 위해 로깅 레벨 체크를 통해 메시지 생성을 제어할 수 있다.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyService {

    // Logger 생성
    private static final Logger logger = LoggerFactory.getLogger(MyService.class);

    public void performTask() {
        logger.trace("TRACE 레벨 메시지");
        logger.debug("DEBUG 레벨 메시지");
        logger.info("INFO 레벨 메시지");
        logger.warn("WARN 레벨 메시지");
        logger.error("ERROR 레벨 메시지");

        try {
            // 예외 발생 가능 코드
        } catch (Exception e) {
            logger.error("예외가 발생했습니다.", e);
        }
    }
}
```

위 예시에서 `Logger` 를 사용해 여러 레벨의 로그 메시지를 기록한다. 각 레벨의 로그는 필요에 따라 출력되며, 로그를 통해 애플리케이션의 상태와 문제를 모니터링 할 수 있다.

## LoggerFactory

`LoggerFactory` 는 **`Logger` 객체를 생성하고 관리**하는 역할을 한다. 이 클래스는 로거 인스턴스를 생성할 때 사용되며, 애플리케이션에서 로거를 일관되게 관리할 수 있도록 도와준다.

### Features

- **Logger 생성**: `LoggerFactory` 는 특정 클래스에 대한 `Logger` 객체를 생성한다. 이때, 클래스의 이름을 로거 이름으로 사용하여, 각 클래스마다 고유한 `Logger` 를 가지게 된다.
- **로깅 프레임워크와 통합**: `LoggerFactory` 는 SLF4J 인터페이스를 통해 다양한 로깅 프레임워크와 통합된다. 이를 통해 개발자는 코드에서 로깅 프레임워크에 종속되지 않고, 필요한 경우 로깅 프레임워크를 쉽게 교체할 수 있다.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyService {

    // LoggerFactory를 사용하여 Logger 생성
    private static final Logger logger = LoggerFactory.getLogger(MyService.class);

    public void performTask() {
        logger.info("작업을 수행합니다.");
    }
}
```

위 예시에서 `LoggerFactory.getLogger(MyService.class)`는 `MyService` 클래스에 대한 로거를 생성한다. 이 로거는 `MyService` 클래스 내에서 사용되며, 클래스 별로 로깅을 관리할 수 있게 해준다.

## Relationship between Logger and LoggerFactory

- **Logger**: 로깅을 실제로 수행하는 객체로, 로그 메시지를 기록하는 메서드를 제공한다.
- **LoggerFactory**: `Logger` 객체를 생성하고 관리하는 역할을 하며, 로거 인스턴스를 중앙에서 관리하도록 도와준다.

이 두 클래스는 함께 사용되며, `LoggerFactory` 를 통해 생성된 `Logger` 는 애플리케이션의 각 클래스에서 로깅을 일관되게 수행할 수 있게 해준다.