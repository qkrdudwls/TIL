# Method Injection

## Problem with Singleton Beans and Prototype Beans

일반적으로 스프링 애플리케이션에서 대부분의 빈은 싱글톤으로 관리된다. 싱글톤 빈 A가 다른 싱글톤 빈 B와 협력하는 경우, 또는 프로토타입 빈이 다른 프로토타입 빈과 협력하는 경우, 이러한 의존성은 빈 A의 속성으로 빈 B를 정의함으로써 쉽게 처리된다. 그러나 싱글톤 빈 A가 매번 호출될 때마다 새로운 프로토타입 빈 B를 필요로 하는 상황이 발생하면 문제가 생긴다. 스프링 컨테이너는 싱글톤 빈 A 를 단 한 번만 생성하므로, 빈 B를 매번 새로 설정해 줄 수 없다.

## Using ApplicationContextAware Interface

이를 해결하기 위한 일반적인 방법은 빈 A가 스프링 컨테이너(ApplicationContext)에 접근할 수 있게 하여, 필요할 때마다 `getBean("B")` 메소드를 호출하여 새로운 빈 B 인스턴스를 가져오는 것이다. 이를 위해 **`ApplicationContextAware` 인터페이스를 구현하고, 스프링 컨테이너에 직접 접근**할 수 있도록 한다. 그러나 이 방법은 빈 A가 스프링 프레임워크에 강하게 의존하게 만들어, **비즈니스 로직과 프레임워크 간의 결합도가 높아진다는 단점**이 있다.

## Method Injection

메소드 주입은 스프링 IoC 컨테이너가 제공하는 고급 기능으로, 위에서 설명한 문제를 해결할 수 있다. 메소드 주입을 통해 **스프링 컨테이너는 빈이 관리하는 메소드를 동적으로 오버라이드**하여, **특정 빈을 반환**하도록 할 수 있다. 특히, 이 기능은 **프로토타입 빈을 다룰 때 유용**하다. 

스프링 프레임워크는 CGLIB 라이브러리를 사용하여 바이트 코드를 동적으로 생성하고, 이를 통해 **메소드를 오버라이드하는 서브클래스를 생성**한다. 따라서 원래 클래스의 메소드가 아닌 동적으로 생성된 서브클래스의 메소드가 호출된다.

 메소드 주입을 사용하면, **스프링 프레임워크에 대한 의존성을 제거하면서도 동적으로 프로토타입 빈을 주입받을 수 있다**. 예를 들어, `CommandManager` 클래스는 더 이상 스프링 API에 의존하지 않으며, `createCommand()`라는 추상 메소드를 선언한다. 이 메소드는 스프링 컨테이너에 의해 동적으로 구현되어, 매번 새로운 `Command` 인스턴스를 반환한다.

XML 기반의 설정에서는 `<lookup-method>` 요소를 사용하여 메소드 주입을 설정할 수 있다. 이 요소를 사용하여, 특정 메소드가 호출될 때마다 스프링 컨테이너가 지정된 프로토타입 빈을 반환하도록 할 수 있다.

```xml
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype"/>
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>
```

이와 같이 설정하면 `CommandManager`의 `createCommand()` 메소드는 매번 호출될 때마다 `myCommand` 프로토타입 빈의 새로운 인스턴스를 반환한다.

## Lookup Method Injection

XML 기반 설정 외에도, 어노테이션 기반으로도 메소드 주입을 설정할 수 있다. `@Lookup` 어노테이션을 사용하여, 메소드 주입을 설정할 수 있다. 

```java
java코드 복사
public abstract class CommandManager {
    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
}

```

`@Lookup` 어노테이션을 사용하면, **특정 빈 이름을 지정하거나, 반환 타입에 맞춰 자동으로 빈을 주입받을 수 있다**.

## Arbitrary Method Replacement

스프링은 기존 메소드를 완전히 다른 메소드로 대체하는 기능도 제공한다. 이는 XML 설정에서 `<replaced-method>` 요소를 사용하여 구현할 수 있다.

```xml
<bean id="myValueCalculator" class="x.y.z.MyValueCalculator">
    <replaced-method name="computeValue" replacer="replacementComputeValue">
        <arg-type>String</arg-type>
    </replaced-method>
</bean>

<bean id="replacementComputeValue" class="a.b.c.ReplacementComputeValue"/>
```

이 설정을 통해 `computeValue(String)` 메소드를 대체하여 새로운 구현체를 사용할 수 있다. 이 방식은 주로 기존 메소드의 구현을 다른 방식으로 대체해야 할 때 사용되지만, 일반적인 경우에는 잘 사용되지 않는다.