# Dependencies and Configuration in Detail

## Straight Values

스프링에서는 `<property/>` 또는 `<constructor-arg/>` 요소의 `value` 속성을 사용해 빈의 속성이나 생성자 인자에 기본 값을 설정할 수 있다. 이때, 스프링의 변환 서비스가 문자열 값을 해당 속성이나 인자의 실제 타입으로 변환해 준다. 아래는 데이터베이스 연결을 설정하는 예시이다.

```xml
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- setDriverClassName(String) 호출 -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="abcdefghijk"/>
</bean>
```

위의 예시에서는 `myDataSource`라는 빈을 정의하고, MySQL 데이터베이스와의 연결을 위한 드라이버 클래스 이름, URL, 사용자 이름, 비밀번호 등의 속성을 설정한다. XML 구성 파일에서 이러한 설정은 문자열로 지정되며, 스프링이 자동으로 이를 적절한 타입으로 변환해 준다. 

`p-namespace`를 사용하면 XML에서 빈 속성을 더 간단하게 정의할 수 있다. 이를 통해 `<property/>` 요소를 사용하지 않고도 속성을 직접 지정할 수 있다.

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="abcdefghijk"/>
</beans>
```

`p-namespace`를 사용하면 구성 파일이 더 간결해지지만, 오타와 같은 오류는 런타임 시에만 발견될 수 있다. 이러한 이유로, IDE에서 자동 완성 기능을 사용하는 것이 권장된다.

### The idref element

`idref` 요소는 다른 빈의 ID(문자열 값)를 안전하게 전달하는 방법이다. 이는 `<constructor-arg/>` 또는 `<property/>` 요소에서 사용된다. 

```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```

위의 예시처럼 하면 `targetName` 속성에 `theTargetBean` 이라는 빈의 ID가 설정된다. `idref`를 사용하면 스프링 컨테이너가 배포 시점에 참조된 빈이 실제로 존재하는지 검증할 수 있으므로, 오타로 인한 오류를 방지할 수 있다. 

## References to Other Beans

`ref` 요소는 `<constructor-arg/>` 또는 `<property/>` 요소 내에서 다른 빈에 대한 참조를 설정하는 데 사용된다. 이를 통해 한 빈의 속성 값을 컨테이너에서 관리되는 다른 빈(collaborators)으로 설정할 수 있다.

```xml
<ref bean="someBean"/>
```

`ref` 요소는 주어진 빈 ID를 가진 다른 빈에 대한 참조를 생성한다. 이를 통해 협력자를 명시적으로 정의하고, 스프링이 해당 협력자를 필요할 때 초기화하도록 할 수 있다. 

## Inner Beans

내부 빈은 `<property/>` 또는 `<constructor-arg/>` 요소 내에 정의된 `<bean/>` 요소이다. 내부 빈은 외부 빈 내에서만 사용되며, 독립적으로 접근하거나 다른 협력자에게 주입할 수 없다.

```xml
<bean id="outer" class="...">
    <property name="target">
        <bean class="com.example.Person"> <!-- 내부 빈 -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```

이러한 내부 빈은 항상 익명이며, 외부 빈과 함께 생성된다. 예를 들어, 이 설정에서 `target` 속성은 `Person` 클래스의 새로운 인스턴스로 설정된다.

### Advantages

- **설정의 가독성**: Inner Bean은 관련 객체를 논리적으로 그룹화하여 설정의 가독성을 높인다.
- **사용 제한**: Bean의 사용 범위를 명확히 하여, 외부에서 불필요하게 접근하는 것을 방지한다.

### Disadvantages

- **재사용 불가**: Inner Bean은 외부에서 사용할 수 없으므로, 같은 유형의 Bean을 여러 곳에서 사용해야 하는 경우 적합하지 않다.
- **복잡성 증가**: 많은 Inner Bean을 사용하면 설정 파일이 복잡해질 수 있다.

## Collections

스프링은 `<list/>`, `<set/>`, `<map/>`, `<props/>` 요소를 사용해 자바 컬렉션 타입(List, Set, Map, Properties 등)의 속성이나 생성자 인자를 설정할 수 있다. 

```xml
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- Properties 설정 -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- List 설정 -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- Map 설정 -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- Set 설정 -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```

각 컬렉션 요소는 `bean`, `ref`, `idref`, `list`, `set`, `map`, `props`, `value`, `null` 등의 하위 요소를 가질 수 있다.

### Collection Merging

스프링 프레임워크에서 제공하는 컬렉션 병합 기능은 부모 빈과 자식 빈 간의 컬렉션 타입 속성을 병합해주는 기능이다.  이를 통해 부모 빈에 정의된 `<list/>`, `<map/>`, `<set/>`, 또는 `<props/>` 같은 컬렉션 요소들을 자식 빈에서 상속받아 활용할 수 있으며, 자식 빈에서 부모의 컬렉션 요소들을 병합하거나 재정의할 수 있고, 자식 컬렉션에서 `merge="true"` 속성을 사용하여 명시적으로 병합을 지정할 수 있다.

```xml
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>

    <bean id="child" parent="parent">
        <property name="adminEmails">
            <!-- 병합은 자식 컬렉션 정의에서 지정됨 -->
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
</beans>
```

위의 예제에서 `parent`라는 부모 빈이 있고, 이 빈에는 `adminEmails`라는 속성에 몇 가지 이메일 주소가 정의되어 있다. 자식 빈인 `child`는 이 `parent` 빈을 상속받으며, 자신의 `adminEmails` 속성에서 부모의 속성 값과 병합을 수행한다.

병합 결과, `child` 빈의 `adminEmails` 속성은 다음과 같은 결과를 가진다.

```graphql
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```

- `administrator`와 `support`는 부모에서 상속받은 값이다.
- `support`는 자식에서 재정의(override) 되었으며, 따라서 최종적으로 `support@example.co.uk` 값이 사용된다.
- `sales`는 자식에서 새롭게 추가된 값이다.

병합 기능은 `list`, `map`, `set`, `props`와 같은 컬렉션 타입에도 동일하게 적용된다. 각각의 경우에 병합은 다음과 같이 동작한다.

- **List**: 부모의 리스트 요소들이 자식 리스트 요소들 앞에 위치한다.
- **Map, Set, Props**: 이러한 타입의 컬렉션은 순서가 없기 때문에, 병합 시 순서에 대한 의미가 없다.

### Limitations of Collection Merging

- 서로 다른 컬렉션 타입 간의 병합은 불가능하다. 예를 들어, `Map`과 `List`를 병합하려고 하면 예외가 발생한다.
- `merge` 속성은 반드시 자식 정의에만 지정되어야 한다. 부모 정의에서 `merge` 속성을 지정하는 것은 의미가 없으며, 원하는 병합이 이루어지지 않는다.

### Strongly-typed Collection

Java에서는 generic 타입을 지원하기 때문에 강한 타입 컬렉션을 사용할 수 있다. 이는 특정 타입의 요소만 포함될 수 있는 컬렉션을 선언할 수 있음을 의미한다. 스프링에서는 이러한 강한 타입 컬렉션을 빈에 주입할 때 타입 변환을 지원한다. 

아래는 `Map<String, Float>` 타입의 강한 타입 컬렉션을 사용한 예제이다.

```java
public class SomeClass {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```

```xml
<beans>
    <bean id="something" class="x.y.SomeClass">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```

이 예제에서는 `accounts` 속성이 `Map<String, Float>` 타입으로 정의되어 있으며, XML에서 정의된 `value` 값들은 문자열(`String`)이지만, 스프링의 타입 변환 기능 덕분에 자동으로 `Float` 타입으로 변환되어 주입된다. 이로 인해 개발자는 더욱 안전하게 강한 타입의 컬렉션을 사용할 수 있다.

## Null and Empty String Values

스프링은 빈 문자열과 `null` 값을 처리할 수 있다. 빈 문자열을 설정하려면 다음과 같이 한다.

```xml
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```

`null` 값을 설정하려면 `<null/>` 요소를 사용한다.

```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```

## XML Shortcut with the p-namespace

**p-namespace**를 사용하면 `<property>` 요소를 사용하지 않고 빈의 속성을 설정할 수 있다. 이 방식은 XML 문서의 가독성을 높이고 코드를 간소화하는 데 유용하다.

아래는 표준 XML 형식을 사용한 빈 설정 예시이다.

```xml
<bean name="classic" class="com.example.ExampleBean">
    <property name="email" value="someone@somewhere.com"/>
</bean>
```

위의 코드를 p-namespace를 사용해 간단히 표현할 수 있다.

```xml
<bean name="p-namespace" class="com.example.ExampleBean" p:email="someone@somewhere.com"/>
```

위 예시에서 `p:email="someone@somewhere.com"` 속성은 `email` 속성에 값을 설정하는 것을 의미한다. 이 방식은 `<property>` 요소를 사용하는 것과 동일한 결과를 제공한다.

p-namespace는 다른 빈을 참조할 때도 사용할 수 있다. 예를 들어, 다른 빈을 참조하는 두 가지 방식은 다음과 같다.

```xml
<!-- 표준 XML 형식 -->
<bean name="john-classic" class="com.example.Person">
    <property name="name" value="John Doe"/>
    <property name="spouse" ref="jane"/>
</bean>

<!-- p-namespace 사용 -->
<bean name="john-modern" class="com.example.Person" p:name="John Doe" p:spouse-ref="jane"/>
```

위 예시에서 `p:spouse-ref="jane"`는 `jane`이라는 다른 빈을 참조하는 것을 의미한다. 속성명 뒤에 `-ref`를 붙여 참조를 표현한다.

### Caution

p-namespace는 편리하지만, 모든 경우에 사용할 수 있는 것은 아니다. 예를 들어, 속성명이 `Ref`로 끝나는 경우, p-namespace의 `-ref`와 충돌할 수 있다. 이러한 경우에는 표준 XML 형식을 사용하는 것이 더 안전하다.

## XML Shortcut with the c-namespace

**c-namespace**는 Spring 3.1에서 도입된 기능으로, 생성자 인자를 설정할 때 `<constructor-arg>` 요소를 사용하지 않고 속성으로 직접 설정할 수 있다. 이는 생성자 기반 의존성 주입을 단순화하는 방법이다.

다음은 생성자 인자를 설정하는 표준 방식이다.

```xml
<bean id="beanOne" class="x.y.ThingOne">
    <constructor-arg name="thingTwo" ref="beanTwo"/>
    <constructor-arg name="thingThree" ref="beanThree"/>
    <constructor-arg name="email" value="something@somewhere.com"/>
</bean>
```

이를 c-namespace를 사용하여 표현하면 다음과 같다.

```xml
<bean id="beanOne" class="x.y.ThingOne" c:thingTwo-ref="beanTwo"
    c:thingThree-ref="beanThree" c:email="something@somewhere.com"/>
```

### **인덱스 기반 설정 (Index-based Configuration)**

드물게 생성자 인자의 이름을 사용할 수 없을 때 인덱스를 사용할 수 있다. 이 경우는 다음과 같다.

```xml
<bean id="beanOne" class="x.y.ThingOne" c:_0-ref="beanTwo" c:_1-ref="beanThree"
    c:_2="something@somewhere.com"/>
```

인덱스 기반 설정에서는 인덱스 번호 앞에 `_`를 붙여야 한다. 이는 XML 속성명이 숫자로 시작할 수 없다는 규칙 때문이다.

## Compound Property  Name

Spring XML에서는 복합 또는 중첩된 속성 이름을 사용할 수 있다. 이는 빈의 특정 속성에 접근하여 값을 설정할 때 유용하다.

```xml
<bean id="something" class="things.ThingOne">
    <property name="fred.bob.sammy" value="123" />
</bean>
```

이 예시에서 `something` 빈은 `fred`라는 속성을 가지고 있으며, `fred`는 다시 `bob`이라는 속성을, `bob`은 `sammy`라는 속성을 가진다. 마지막 `sammy` 속성에 `123`이라는 값을 설정하게 된다. 이 방법을 사용할 때, 경로에 있는 모든 속성(`fred`, `bob`)이 null이 아니어야 하며, 그렇지 않으면 `NullPointerException`이 발생할 수 있다.