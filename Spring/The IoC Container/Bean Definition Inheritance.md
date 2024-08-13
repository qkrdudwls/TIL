# Bean Definition Inheritance

스프링에서는 빈 정의 상속을 통해 **부모 빈의 공통 속성이나 로직을 여러 자식 빈에서 재사용**할 수 있고, 필요에 따라 **자식 빈에서 특정 속성을 재정의하거나 추가**할 수 있다. 빈 정의 상속은 코드 중복을 줄이고 유지 보수를 용이하게 만들어 XML 기반 설정에서 유용하지만, 부모 빈이 실제로 인스턴스화 되지 않도록 하기 위해 추상적으로 지정하여 템플릿으로만 사용하도록 하는 것이 중요하다. 이러한 개념은 대규모 애플리케이션에서 빈 설정을 관리하는 데 매우 유용하며, 효율적인 관리를 가능하게 한다.

## Bean Definition and Bean Definition Inheritance

**빈 정의**는 스프링 애플리케이션에서 빈이라는 객체를 생성하고 관리하는 데 필요한 모든 정보를 담고 있는 요소이다. 이 정보에는 생성자 매개변수, 속성 값, 초기화 메서드, 소멸 메서드 등 다양한 설정이 포함된다.

**빈 정의 상속**은 이러한 빈 정의를 재사용할 수 있도록 도와주는 기능이다. 상속을 통해 기본적으로 공통된 설정을 부모 빈에 정의해두고, 자식 빈이 그 설정을 상속받아 사용한다. 자식 빈은 필요한 경우 부모의 설정을 재정의하거나 추가적인 설정을 덧붙일 수 있다.

## Bean Definition Inheritance in XML

스프링에서는 보통 XML 파일을 사용해 빈 정의를 설정한다. 이때 부모 빈과 자식 빈 사이의 상속 관계를 설정할 수 있다.

### Parent Bean Definition

부모 빈 정의는 다른 빈들의 **템플릿** 역할을 하는데, 이 템플릿에는 여러 공통 설정이 담겨 있다. 예를 들어, 아래 코드는 부모 빈을 정의하는 방법을 보여준다.

```xml
<bean id="inheritedTestBean" abstract="true"
		class="org.springframework.beans.TestBean">
	<property name="name" value="parent"/>
	<property name="age" value="1"/>
</bean>
```

- **id**: 빈의 고유한 식별자이다.
- **abstract="true"**: 이 속성은 이 빈이 추상적인 빈임을 나타낸다. 추상 빈은 직접 인스턴스화될 수 없으며, 오직 자식 빈이 상속하여 사용하기 위한 템플릿 역할만 한다.
- **class**: 빈이 생성할 객체의 클래스이다.
- **property**: 빈이 관리할 속성들을 정의한다.

이 부모 빈은 `name`과 `age`라는 속성을 가지고 있으며, 이 속성들은 자식 빈에서 상속될 수 있다.
만약 부모 빈 정의가 추상적이지 않다면, Spring 컨테이너는 이 빈을 실제로 인스턴스화 하려고 시도할 수 있다. 하지만 부모 빈은 주로 템플릿 역할을 하기 때문에 직접 사용되지 않도록 **abstract** 속성을 **true**로 설정해야 한다. 그렇지 않으면 **ApplicationContext**가 싱글톤 빈을 미리 인스턴스화 할 때 오류가 발생할 수 있다.

### Child Bean Definition

자식 빈은 부모 빈으로부터 속성들을 상속받고, 필요에 따라 일부 속성을 재정의할 수 있다. 자식 빈의 예제는 아래와 같다.

```xml
<bean id="inheritsWithDifferentClass"
		class="org.springframework.beans.DerivedTestBean"
		parent="inheritedTestBean" init-method="initialize">
	<property name="name" value="override"/>
	<!-- 부모로부터 age 속성 값 1을 상속받음 -->
</bean>
```

- **parent**: 상속받을 부모 빈의 **id**를 지정한다. 이 속성을 통해 자식 빈이 부모 빈의 설정을 상속받는다.
- **class**: 자식 빈이 생성할 클래스이다. 부모 빈과 다른 클래스를 지정할 수 있으며, 이 경우 자식 클래스는 부모 클래스의 속성 설정을 수용할 수 있어야 한다.
- **init-method**: 빈이 초기화될 때 호출할 메서드를 지정한다.
- **property**: 부모 빈의 속성을 재정의할 수 있다. 위 예제에서는 `name` 속성이 `parent`에서 `override`로 재정의되었다.

이 자식 빈은 부모 빈의 `age` 속성 값을 그대로 상속받고, `name` 속성만 `override`로 변경한다.

## Details of Bean Definition Inheritance

- **상속받는 설정**: 자식 빈은 부모 빈으로부터 **scope**(범위), **constructor argument values**(생성자 인자 값), **property values**(속성 값), **method overrides**(메서드 재정의)를 상속받는다.
- **덮어쓰는 설정**: 자식 빈에서 새로 지정한 **스코프, 초기화 메서드, 소멸 메서드, 정적 팩토리 메서드** 설정은 부모 빈의 해당 설정을 덮어쓴다
- **항상 자식 정의가 우선시되는 설정**: `depends-on`, `autowire mode`, `dependency check`, `singleton`, `lazy-init` 등의 설정은 항상 자식 빈 정의의 설정이 우선시된다.