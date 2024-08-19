# Spring MVC Pattern

스프링 MVC(Spring Model-View-Controller) 패턴은 **스프링 프레임워크에서 웹 애플리케이션을 개발할 때 사용하는 설계 패턴**이다. 이 패턴은 애플리케이션의 비즈니스 로직, 사용자 인터페이스, 그리고 입력 데이터를 **독립적으로 관리하여 유지보수성을 높이고 확장성을 향상시키는 데 목적**이 있다. MVC 패턴은 크게 모델(Model), 뷰(View), 그리고 컨트롤러(Controller)로 구성된다.

## Model

모델은 애플리케이션의 **핵심 비즈니스 로직을 담당**하며, **데이터 상태를 관리**한다. 데이터베이스와의 상호작용, 비즈니스 규칙 적용, 데이터의 가공 등이 모델에서 처리된다. 스프링에서는 일반적으로 **`@Service` , `@Repository` 등의 어노테이션을 사용**하여 비즈니스 로직과 데이터 접근 로직을 구현한다. 모델은 뷰나 컨트롤러에 종속되지 않으며, 독립적으로 존재한다.

### Service

**Service**는 **비즈니스 로직을 담당**하는 계층이다. `Controller`가 요청을 받으면, `Service` 계층은 그 요청에 맞는 비즈니스 로직을 수행한다. 이 계층은 애플리케이션의 핵심적인 로직을 구현하며, 데이터 접근이나 기타 외부 API 호출 등이 필요할 경우 `Repository`  또는 다른 서비스와 상호작용한다.

#### 주요 기능

- 비즈니스 로직 처리.
- 데이터를 조작하거나, 다른 서비스와의 통신 수행.
- 트랜잭션 관리.

#### 주요 어노테이션

- `@Service`: 클래스가 서비스 역할을 함을 명시.
- `@Transactional`: 메서드나 클래스에 트랜잭션 경계를 정의

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User getUserById(Long id) {
        return userRepository.findById(id).orElseThrow(() -> new ResourceNotFoundException("User not found"));
    }
}
```

### Repository

**Repository**는 **데이터베이스와의 상호작용을 담당하는 계층**이다. 이 계층은 데이터를 저장, 검색, 수정, 삭제하는 CRUD 작업을 수행한다. 스프링에서는 주로 JPA 또는 MyBatis와 같은 ORM 프레임워크를 사용하여 데이터베이스 작업을 쉽게 처리할 수 있다.

#### 주요 기능

- 데이터베이스와의 CRUD 작업 수행.
- 데이터 접근 로직을 캡슐화.
- `Service` 계층에서 요청한 데이터의 검색 및 저장 작업 수행.

#### 주요 어노테이션

- `@Repository`: 클래스가 데이터 액세스 계층(DAO)임을 명시.
- `@Query`: JPA에서 직접 SQL 쿼리를 작성할 때 사용.

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // JPA가 제공하는 기본 CRUD 메서드 외에도 사용자 정의 쿼리 메서드 정의 가능
    Optional<User> findByUsername(String username);
}
```

## View

뷰는 **사용자에게 데이터를 표시**하는 역할을 한다. 사용자가 볼 수 있는 웹 페이지나 화면을 생성하며, 사용자 인터페이스 요소를 담당한다. 스프링 MVC에서는 JSP, Thymeleaf, Freemarker 등의 템플릿 엔진을 사용하여 뷰를 생성할 수 있다. 뷰는 **모델에서 제공된 데이터를 사용하여 사용자에게 정보를 출력**하고, **사용자로부터 받은 입력을 컨트롤러에 전달**한다.

## Controller

컨트롤러는 **사용자의 요청을 처리**하고, **모델과 뷰를 연결하는 역할**을 한다. 사용자가 요청을 보내면, 컨트롤러는 해당 요청을 처리하고 필요한 비즈니스 로직을 수행하도록 모델에 지시한다. 그런 다음, 모델에서 처리된 데이터를 바탕으로 적절한 뷰를 선택하고, 그 뷰에 데이터를 전달하여 사용자에게 응답한다. 스프링에서 컨트롤러는 **`@Controller`나 `@RestController` 어노테이션을 사용**하여 구현된다.

#### 주요 기능

- 사용자의 HTTP 요청(GET, POST, PUT, DELETE 등)을 받아 처리.
- `Service` 계층을 호출하여 비즈니스 로직을 수행.
- 모델 데이터를 뷰에 전달하여 사용자에게 응답 생성.

#### 주요 어노테이션

- `@Controller`: 클래스가 컨트롤러 역할을 함을 명시.
- `@RequestMapping`: 특정 URL 경로와 이 컨트롤러 메서드를 매핑.
- `@GetMapping`, `@PostMapping` 등: HTTP 메서드별 매핑.
- `@RestController`: JSON/XML 형태의 데이터를 반환하는 RESTful 웹 서비스 컨트롤러를 정의.

```java
@RestController
@RequestMapping("/api")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/users/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);
    }
}
```
### `@Controller`

`@Controller` 는 스프링 MVC에서 주로 사용되는 어노테이션으로, 웹 요청을 처리하는 컨트롤러 클래스를 나타낸다. 이 어노테이션을 사용하면 스프링이 해당 클래스를 컨트롤러로 인식하고, HTTP 요청을 처리할 수 있다.

#### 주요 특징

- **뷰 리졸버(View Resolver)**: `@Controller`는 주로 뷰 이름을 반환하는데 사용된다. 메서드가 문자열을 반환하면, 스프링은 이 문자열을 뷰 이름으로 해석하고, 해당 뷰를 찾아 렌더링한다.
- **모델**: 뷰에 데이터를 전달하기 위해 `Model` 또는 `ModelAndView` 객체를 사용할 수 있다. 이 데이터는 뷰에서 사용된다.
- **전형적인 MVC 패턴**: `@Controller`는 전통적인 스프링 MVC 패턴에서 사용되며, 주로 서버 측에서 HTML을 렌더링하여 클라이언트에게 반환하는 방식으로 사용된다.

### `@RestController`

`@RestController`는 `@Controller`와 `@ResponseBody`를 결합한 어노테이션올, 주로 RESTful 웹 서비스를 개발할 때 사용된다. 이 어노테이션을 사용하면, 스프링이 메서드에서 반환하는 객체를 JSON이나 XML 형태로 변환하여 HTTP 응답 본문에 직접 작성한다.

#### 주요 특징

- **자동으로 JSON/XML 변환**: 메서드에서 반환된 데이터는 `HttpMessageConverter`에 의해 JSON 또는 XML 등의 포맷으로 자동 변환된다.
- **뷰가 필요 없음**: `@RestController`는 주로 데이터 자체를 반환하므로, 별도의 뷰 리졸버가 필요하지 않는다.
- **RESTful 웹 서비스**: 주로 RESTful API를 개발할 때 사용되며, 클라이언트는 서버로부터 HTML이 아닌 JSON/XML 등의 데이터 포맷을 응답받는다.

## Interaction between Controller, Service, and Repository

- **Controller**는 사용자의 요청을 받고, **Service**에 비즈니스 로직 처리를 위임한다.
- **Service**는 **Repository**를 이용해 데이터베이스와 상호작용하며 필요한 데이터를 가져오거나 저장한다.
- 최종적으로 **Controller**는 **Service**로부터 처리된 결과를 받아 사용자에게 응답으로 반환한다.

## Spring MVC Flow

1. **사용자 요청**: 사용자가 웹 브라우저를 통해 요청을 보내면, 해당 요청은 스프링 디스패처 서블릿(DispatcherServlet)에 도달한다.
2. **디스패처 서블릿**: 디스패처 서블릿은 요청을 분석하고, 요청에 적합한 컨트롤러를 찾아서 요청을 전달한다.
3. **컨트롤러**: 컨트롤러는 비즈니스 로직을 처리하기 위해 모델과 상호작용하며, 필요한 데이터를 가져오거나 저장한다.
4. **모델**: 모델은 컨트롤러의 요청에 따라 데이터를 처리하고, 그 결과를 컨트롤러에 반환한다.
5. **뷰 선택 및 데이터 전달**: 컨트롤러는 모델에서 반환된 데이터를 바탕으로 적절한 뷰를 선택하고, 그 뷰에 데이터를 전달한다.
6. **뷰 렌더링**: 선택된 뷰는 모델 데이터를 사용하여 HTML 등의 사용자 인터페이스를 생성하고, 이를 사용자에게 반환한다.
7. **응답 전송**: 최종적으로 생성된 응답이 사용자에게 전송되며, 사용자는 웹 브라우저에서 결과를 확인한다.