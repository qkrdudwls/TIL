# Dependencies

# Dependency Injection

의존성 주입(Dependency Injection, DI)은 **객체가 필요로 하는 의존성을 직접 생성하지 않고 외부에서 주입받는 설계 패턴**을 말한다. 즉, 객체가 사용하는 다른 객체들을 생성자 인자, 팩토리 메서드 인자, 또는 객체 인스턴스의 속성으로 전달 받아 주입된다. 이는 객체가 스스로 의존성을 찾거나 생성하지 않고 외부에서 주입받도록 하여, 제어의 역전(Inversion of Control, IoC)을 구현하는 기법 중 하나다.

## Advantages

- **코드의 간결성**: DI를 사용하면 객체가 스스로 의존성을 찾지 않기 때문에 코드가 더 간결해진다.
- **디커플링**: 객체와 의존성 사이의 결합도가 낮아지기 때문에, 클래스 간의 의존성이 줄어들어 더 유연하고 테스트하기 쉬운 코드가 된다.
- **테스트 용이성**: DI를 사용하면 테스트 시 의존성을 쉽게 모킹(Mock)하거나 스텁(Stub)으로 대체할 수 있어, 단위 테스트가 더 쉬어진다.

## Types

1. **생성자 기반 의존성 주입(Constructor-based DI)**
- 생성자 기반 DI는 객체가 생성될 때 필요한 의존성을 생성자의 인자로 주입받는 방식이다.
- 아래 예제에서는 `SimpleMovieLister` 클래스는 `MovieFinder`라는 의존성을 가지며, 이는 생성자를 통해 주입된다.
        
    ```java
    public class SimpleMovieLister {
        private final MovieFinder movieFinder;
    
        public SimpleMovieLister(MovieFinder movieFinder) {
            this.movieFinder = movieFinder;
        }
    }
    ```
        
    이 방식은 **클래스가 POJO(Plain Old Java Object)로 유지**되며, 특정 컨테이너에 종속되지 않는다.
        
2. **세터 기반 의존성 주입(Setter-based DI)**
- 세터 기반 DI는 객체가 생성된 후 세터 메서드를 통해 의존성을 주입받는 방식이다.
- 아래 예제에서는 `SimpleMovieLister` 클래스는 `MovieFinder` 의존성을 세터 메서드를 통해 주입받는다.
        
    ```java
    public class SimpleMovieLister {
        private MovieFinder movieFinder;
    
        public void setMovieFinder(MovieFinder movieFinder) {
            this.movieFinder = movieFinder;
        }
    }
    ```
        
## Constructor-based DI vs Setter-based DI

- **생성자 기반 DI**
    - 필수적인 의존성을 주입하는데 적합하다.
    - 객체가 불변(Immutable) 상태를 가질 수 있도록 하며, 주입된 모든 의존성이 null이 아님을 보장할 수 있다.
    - 많은 생성자 인자를 가지는 경우, 클래스가 너무 많은 책임을 가지는지 확인이 필요하다.
- **세터 기반 DI**
    - 선택적인 의존성을 주입하는데 적합합다.
    - 나중에 객체의 의존성을 재설정하거나 재구성할 수 있는 유연성을 제공한다.
    - JMX MBeans와 같은 관리 시나리오에서 유용하게 사용될 수 있다.

## Dependency Resolution Process

스프링의 ApplicationContext는 configuration metadata를 기반으로 객체의 의존성을 해결하고 주입한다. 이 metadata는 XML, Java 코드, 또는 어노테이션으로 제공될 수 있으며, 각 객체의 의존성은 property, 생성자 인자, 또는 정적 팩토리 메서드 인자로 표현된다. 

## Circular Dependecy

순환 의존성은 **두 개 이상의 객체가 서로를 직접 또는 간접적으로 의존하는 상황**을 말한다. 이는 객체 간의 관계가 닭과 달걀의 관계처럼 서로 얽혀 있는 상태로, 클래스 A가 클래스 B를 필요로 하는 동시에 클래스 B가 클래스 A를 필요로 하는 경우가 이에 해당한다. 

스프링 IoC 컨테이너는 객체를 생성하고 주입할 때, 각 객체의 의존성을 해결하고 주입해준다. 하지만 순환 의존성이 발생하면, 스프링이 이러한 의존성을 해결하는 과정에서 문제가 발생할 수 있다. 특히 생성자 기반 의존성 주입을 사용하는 경우, 순환 의존성은 해결할 수 없는 문제를 야기한다. 스프링 컨테이너가 순환 의존성을 감지하면, `BeanCurrentlyInCreationException` 예외를 발생시킨다.

### Solutions

1. **세터 기반 DI 사용**
- 순환 의존성을 해결하는 가장 일반적인 방법은 생성자 주입 대신 세터 주입을 사용하는 것이다. 세터 기반 의존성 주입은 객체를 생성한 후에 의존성을 주입하기 때문에, 순환 의존성이 발생해도 문제가 되지 않는다.
- 클래스 A와 B가 서로를 필요로 할 때, 둘 다 생성자는 기본 생성자를 사용하고, 의존성은 세터 메서드를 통해 주입받도록 설계할 수 있다.
        
    ```java
    public class A {
        private B b;
        
        public void setB(B b) {
            this.b = b;
        }
    }
        
    public class B {
        private A a;
        
        public void setA(A a) {
            this.a = a;
        }
    }
    ```
        
    위와 같이 작성하면 Spring 컨테이너는 A와 B를 모두 생성한 후에 서로의 의존성을 세터를 통해 주입할 수 있다.
        
2. **@Lazy 어노테이션 사용**
- 스프링에서 `@Lazy` 어노테이션을 사용하여 지연 초기화(Lazy Initialization)를 적용할 수 있다. `@Lazy` 어노테이션은 해당 빈(bean)의 초기화를 지연시켜, 실제로 필요할 때까지 빈을 생성하지 않도록 한다.
- 이를 통해 순환 의존성 문제를 피할 수 있다. 예를 들어, A 또는 B 중 하나에 `@Lazy`를 적용하면, 그 빈은 처음 주입될 때가 아닌, 실제로 사용될 때 초기화되므로 순환 의존성을 해소할 수 있다.
        
    ```java
    public class A {
        private final B b;
        
        public A(@Lazy B b) {
            this.b = b;
        }
    }
        
    public class B {
        private final A a;
        
        public B(A a) {
            this.a = a;
        }
    }
    ```
        
3. **설계 변경**
- 순환 의존성은 설계 상의 문제가 될 수 있으므로, 이를 완전히 피하기 위해 애플리케이션의 구조를 재설계하는 것이 필요할 수 있다.
- 객체 간의 의존성을 줄이거나, 중간에 의존성을 끊어줄 수 있는 새로운 계층을 추가하여 순환 의존성을 피할 수 있다.
4. **인터페이스 또는 이벤트 발행 사용**
- 인터페이스를 사용하거나 이벤트 기반 시스템을 도입하여 의존성을 간접적으로 만들 수 있다. 이를 통해 순환 의존성을 우회할 수 있다.
- 예를 들어, A가 B에 의존하는 대신, A가 B가 구현하는 인터페이스에 의존하게 하거나, A가 이벤트를 발행하고 B가 그 이벤트를 구독하도록 설계할 수 있다.
