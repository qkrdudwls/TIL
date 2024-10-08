# Autowiring Collaborators

스프링 컨테이너는 **협업하는 빈 간의 관계를 자동으로 연결**할 수 있다. 이를 통해, 스프링의 ApplicationContext의 내용을 검사하여 빈에 필요한 다른 빈들을 자동으로 해결할 수 있다. 

Autowiring은 다음과 같은 장점이 있다.

- 자동 연결을 통해 속성이나 생성자 인수를 지정할 필요가 크게 줄어든다.
- 자동 연결을 통해 객체가 발전함에 따라 설정을 업데이트할 수 있다.

XML 기반의 configuration metadata를 사용하는 경우, `<bean/>` 요소의 autowire 속성을 사용하여 빈 정의에 대한 자동 연결 모드를 지정할 수 있다. 자동 연결 기능에는 네 가지 모드가 있으며, 각 빈에 대해 자동 연결을 지정할 수 있으므로 어떤 빈을 자동으로 연결할지 선택할 수 있다.

## Autowiring modes

### no

**기본값**으로 **자동 연결이 없다**. 빈 참조는 ref 요소로 정의해야 하고, 기본 설정을 변경하는 것은 더 큰 배포에서는 권장되지 않으며, 협력자를 명시적으로 지정하는 것이 더 큰 제어와 명확성을 제공하기 때문에 어느 정도 시스템의 구조를 문서화할 수 있다.

### byName

**속성 이름에 의한 자동 연결**이다. 스프링은 자동으로 연결해야 하는 속성과 동일한 이름을 가진 빈을 찾아 설정한다. 예를 들어, 빈 정의가 byName으로 설정되어 있고 master 속성이 포함되어 있다면(Spring은 setMaster(..) 메서드를 가짐), 스프링은 master라는 이름의 빈 정의를 찾아 속성을 설정한다.

### byType

**속성 유형에 따라 자동 연결을 수행**한다. 컨테이너에 해당 속성 유형의 빈이 정확히 하나만 존재하는 경우에만 연결되는데, 만약 **여러 개가 존재하면 치명적인 예외가 발생**하기 때문에 해당 빈에 대해 byType 자동 연결을 사용할 수 없다. 일치하는 빈이 없으면 아무런 작업도 수행되지 않으며 속성은 설정되지 않는다.

### constructor

byType과 유사하지만, **생성자 인수에 적용**된다. 컨테이너에 생성자 인수 유형의 빈이 정확히 하나만 존재하지 않는 경우 치명적인 오류가 발생한다.

byType이나 constructor 자동 연결 모드를 사용할 경우, 배열 및 타입이 지정된 컬렉션을 연결할 수 있다. 이 경우, 예상되는 유형과 일치하는 모든 자동 연결 후보가 의존성을 충족시키기 위해 제공된다. 또한, 예상 키 유형이 String인 경우 강력하게 타입이 지정된 Map 인스턴스를 자동 연결할 수 있다. 자동 연결된 Map 인스턴스의 값은 예상 유형과 일치하는 모든 빈 인스턴스로 구성되며, Map 인스턴스의 키는 해당 빈 이름을 포함한다.

## Limitaitons and Disadvantages of Autowiring

자동 연결은 프로젝트 전반에 걸쳐 일관되게 사용할 때 가장 효과적이다. 

다음은 자동 연결의 제한 사항 및 단점이다.

- **속성 및 생성자 인수 설정의 명시적인 의존성은 항상 자동 연결을 무시한다.** 간단한 속성(기본형, 문자열, 클래스 및 이들의 배열)은 자동으로 연결할 수 없다. 이는 설계 상 제한이다.
- **자동 연결은 명시적 연결보다 정확성이 떨어진다.** 스프링은 모호성이 발생할 수 있는 경우 예기치 않은 결과를 방지하기 위해 신중하게 추측을 피하기 때문에 스프링으로 관리되는 객체들 간의 관계는 더 이상 명시적으로 문서화 되지 않는다.
- **스프링 컨테이너로부터 문서를 생성하는 도구에 연결 정보가 제공되지 않을 수 있다.**
- **컨테이너 내에 여러 빈 정의가 설정하려는 속성 메서드나 생성자 인수에서 지정한 유형과 일치할 수 있다.** 배열, 컬렉션, 또는 Map 인스턴스의 경우에는 문제가 되지 않지만, 단일 값을 기대하는 의존성의 경우 이 모호성은 임의로 해결되지 않는다. 유일한 빈 정의가 없으면 예외가 발생한다.

위와 같은 상황에서 몇 가지 옵션이 있다.

- 자동 연결을 포기하고 명시적 연결로 전환한다.
- 자동 연결 후보를 제외하기 위해 빈 정의의 autowire-candidate 속성을 false로 설정한다.
- `<bean/>` 요소의 primary 속성을 true로 설정하여 특정 빈 정의를 기본 후보로 지정한다.
- 주석 기반의 설정을 사용하여 더 세밀한 제어를 구현한다.

## Excluding Beans from Autowiring

빈별로 자동 연결에서 특정 빈을 제외할 수 있다. 스프링의 XML 형식에서, `<bean/>` 요소의 **autowire-candidate 속성을 false**로 설정하면 된다. 그러면 해당 빈 정의는 자동 연결 인프라에서 사용할 수 없게 된다.

autowire-candidate 속성은 타입 기반의 자동 연결에만 영향을 주도록 설계되었다. 이름에 의한 명시적 참조는 여전히 해결되며, 지정된 빈이 자동 연결 후보로 표시되지 않은 경우에도 적용된다. 따라서 이름에 의한 자동 연결은 이름이 일치하는 경우 여전히 빈을 주입한다.

빈 이름에 대한 **패턴 매칭을 기반으로 자동 연결 후보를 제한**할 수도 있다. 최상위 `<beans/>` 요소는 default-autowire-candidates 속성 내에서 하나 이상의 패턴을 허용한다. 예를 들어, 이름이 'Repository'로 끝나는 모든 빈을 자동 연결 후보로 제한하려면 값으로 '*Repository'를 제공하면 된다. 여러 패턴을 제공하려면 쉼표로 구분된 목록으로 정의한다. 빈 정의의 autowire-candidate 속성에 명시적으로 true 또는 false 값을 설정한 경우 항상 우선 적용된다. 이러한 빈에 대해서는 패턴 매칭 규칙이 적용되지 않는다.

이 기술은 다른 빈에 의해 자동 연결되는 것을 원하지 않는 빈에 유용하다. 자동 연결에서 제외된 빈 자체가 자동 연결을 통해 구성되지 못한다는 의미는 아니고, 단지 해당 빈이 다른 빈을 자동 연결하는 후보로 간주되지 않을 뿐이다.
