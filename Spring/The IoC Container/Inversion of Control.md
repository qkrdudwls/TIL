# Inversion of Control
**Inversion of Control**은 프로그래머가 작성한 프로그램이 재사용 라이브러리의 흐름 제어를 받게 되는 소프트웨어 디자인 패턴을 말한다. 전통적인 프로그래밍에서 흐름은 프로그래머가 작성한 프로그램이 외부 라이브러리의 코드를 호출해 이용하지만 IoC가 적용된 구조에서는 외부 라이브러리의 코드가 프로그래머가 작성한 코드를 호출한다. 
## IoC의 목적
* 작업을 구현하는 방식과 작업 수행 자체를 분리한다.
* 모듈을 제작할 때, 모듈과 외부 프로그램의 결합에 대해 고민할 필요 없이 모듈의 목적에 집중할 수 있다. 
* 다른 시스템이 어떻게 동작할 지에 대해 고민할 필요 없이, 미리 정해진 협약대로만 동작하게 하면 된다.
* 모듈을 바꾸어도 다른 시스템에 부작용을 일으키지 않는다. 