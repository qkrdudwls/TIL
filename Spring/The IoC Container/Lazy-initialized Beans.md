# Lazy-initialzied Beans

스프링 프레임워크에서 빈의 초기화는 기본적으로 **즉시 초기화**로 이루어진다. 
이는 `ApplicationContext`가 시작될 때, 모든 싱글톤 빈을 미리 생성하고 구성하는 것을 의미한다. 이 방식은 일반적으로 바람직한데, 그 이유는 초기화 시점에서 구성 오류나 환경 문제를 즉시 발견할 수 있기 때문이다. 하지만 모든 빈을 시작 시점에 미리 생성하는 것이 항상 바람직하지 않은 경우도 있다. 이럴 때, 특정 빈을 **지연 초기화** 하도록 설정할 수 있다.

## Concept

지연 초기화된 빈은 애플리케이션이 시작될 때가 아니라, 해당 **빈이 처음으로 요청될 때 빈 인스턴스를 생성**하는 방식이다. 이를 통해 애플리케이션 시작 시 불필요한 리소스 낭비를 줄이고, 성능을 최적화 할 수 있다. 예를 들어, 생성 비용이 높은 객체가 애플리케이션의 특정 시점에만 필요하다면, 이 빈을 지연 초기화하여 필요할 때만 생성되도록 할 수 있다. 

## Using the Lazy-initialized Attribute in XML

XML 설정에서 `lazy-init` 속성을 사용하여 지연 초기화를 설정할 수 있다. 

```xml
<bean id="lazy" class="com.something.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.something.AnotherBean"/>
```

위 예제에서 `lazy`라는 ID를 가진 빈은 `lazy-init="true"`로 설정되어 있어, `ApplicationContext`가 시작될 때 미리 생성되지 않는다. 대신 이 빈은 처음으로 요청될 때 생성된다. 반면, `not.lazy`라는 이름을 가진 빈은 지연 초기화 설정이 없으므로, `ApplicationContext`가 시작될 때 즉시 초기화된다.

## Dependencies and Lazy-initialization of Singleton Beans

지연 초기화된 빈이 지연 초기화되지 않은 singleton 빈의 의존성으로 설정된 경우, **`ApplicationContext`는 지연 초기화된 빈을 애플리케이션 시작 시점에 생성**한다. 이는 singleton 빈의 의존성을 충족시켜야 하기 때문이다. 예를 들어, 지연 초기화된 빈이 다른 singleton 빈에 주입되어야 한다면, 이 빈은 즉시 초기화된다.

## Controlling Lazy-initialization at the Container Level

지연 초기화는 개별 빈 설정뿐만 아니라, `default-lazy-init` 속성을 사용하여 **컨테이너 수준에서 전체적으로 제어**할 수도 있다. 

```xml
<beans default-lazy-init="true">
    <!-- 모든 빈이 지연 초기화됩니다... -->
</beans>
```

위 예제에서는 `beans` 요소에 `default-lazy-init="true"`를 설정하여, 해당 컨텍스트 내의 모든 빈이 기본적으로 지연 초기화되도록 지정할 수 있다. 이는 개별적으로 `lazy-init` 속성을 설정할 필요 없이, 전체 애플리케이션 컨텍스트에서 지연 초기화를 적용하는 방법이다.