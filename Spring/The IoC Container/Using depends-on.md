# Using depends-on

스프링 프레임워크에서 `depends-on` 속성은 **빈 간의 의존성을 명시적으로 설정**하는 데 사용된다. 보통 한 빈이 다른 빈에 의존할 때는 해당 빈이 속성으로 설정되며, XML 기반 설정 metadata에서 `<ref/>` 요소를 사용하여 이를 달성한다. 그러나 때로는 빈 간의 의존성이 직접적으로 연결되지 않은 경우도 있다. 예를 들어, 데이터베이스 드라이버 등록과 같은 작업을 위해 클래스의 정적 초기화자가 trigger 되어야 하는 경우가 있다.

`depends-on` 속성은 **특정 빈이 초기화되기 전에 다른 하나 이상의 빈이 먼저 초기화되도록 강제**할 수 있다. 이는 직접적인 속성 의존성이 아니라도, 특정 작업 순서를 보장해야 할 때 유용하다.

```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```

위 예제에서, `beanOne`은 `manager`라는 빈이 먼저 초기화된 후에 초기화된다. 이때 `depends-on` 속성을 통해 `beanOne`이 `manager` 빈에 의존성을 가지고 있음을 명시한다.

`depends-on` 속성은 여러 개의 빈 이름을 값으로 제공하여 **다중 의존성을 표현**할 수 있다. 이때, 쉼표(,), 공백, 세미콜론(;)을 구분자로 사용할 수 있다.

```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```

위 예제에서 `beanOne`은 `manager`와 `accountDao` 두 빈이 모두 초기화된 후에 초기화된다. `depends-on` 속성에 두 개 이상의 빈 이름을 나열함으로써 `beanOne`이 두 빈에 의존성을 가지고 있음을 나타낸다.

`depends-on` 속성은 빈의 초기화 순서뿐만 아니라, **소멸 순서**도 제어할 수 있다. 이는 **Singleton 빈**에만 적용되며, 소멸 시간에는 `depends-on` 관계를 가진 빈들이 먼저 소멸된다. 따라서, `depends-on` 속성을 통해 빈의 **종료 순서**를 제어할 수 있다.