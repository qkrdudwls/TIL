# Servlet

서블릿은 Java를 기반으로 웹 애플리케이션을 개발할 때 서버 측에서 동작하는 프로그램이다. 서블릿은 **클라이언트로부터의 요청을 처리**하고, **그에 대한 응답을 생성하여 클라이언트에게 전송**하는 역할을 한다.

## DispatcherServlet

`DispatcherServlet`은 스프링 MVC의 **프론트 컨트롤러**로서, 웹 애플리케이션으로 들어오는 모든 요청을 중앙에서 받아 처리한다. 프론트 컨트롤러는 **요청을 적절한 컨트롤러로 라우팅**하고, **그 결과를 바탕으로 클라이언트에게 응답을 생성**하는 역할을 한다. 

`DispatcherServlet` 은 스프링의 **ApplicationContext와 연결**되어 있다. 이를 통해, 컨트롤러, 서비스, 리포지토리 등의 스프링 빈을 관리하고, 의존성 주입을 통해 각 컴포넌트가 필요한 자원을 사용할 수 있도록 한다. 

스프링 웹 애플리케이션에서 `DispathcerServlet` 은 다음과 같이 동작한다.

- **요청 수신**: 클라이언트의 모든 HTTP 요청은 먼저 `DispatcherServlet`으로 전달된다. 이 서블릿은 애플리케이션의 모든 HTTP 요청을 처리하도록 설정된다.
- **요청 라우팅**: `DispatcherServlet`은 요청 URL과 HTTP 메서드를 바탕으로 적절한 컨트롤러와 메서드를 찾아 호출한다. 이 과정에서 `HandlerMapping`이 중요한 역할을 한다.
- **응답 처리**: 컨트롤러 메서드에서 처리된 결과는 `DispatcherServlet`을 통해 클라이언트로 전송된다. 이때 `ViewResolver`를 사용하여 응답에 사용될 뷰를 결정하고, 그 뷰를 렌더링한다.

만약 스프링 부트를 사용한다면, 별도로 `web.xml` 파일에서 `DispatcherServlet` 을 설정하지 않아도 자동으로 설정된다. 하지만 전통적인 방식으로 설정하려면 `web.xml` 에서 다음과 같이 정의할 수 있다.

```xml
<web-app>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

## Lifecycle

서블릿의 생명 주기는 서블릿 컨테이너에 의해 관리된다. `DispatcherServlet` 도 마찬가지로 생명 주기를 따르며, 다음과 같이 동작한다.

- **생성 (Initialization)**: 서블릿 컨테이너가 `DispatcherServlet` 인스턴스를 생성한다. 이때 `init()` 메서드가 호출되며, 스프링 애플리케이션 컨텍스트가 초기화되고, 필요한 빈들이 로드된다.
- **요청 처리 (Request Handling)**: 클라이언트의 HTTP 요청이 들어오면 `service()` 메서드가 호출되어 요청을 처리한다. `DispatcherServlet`은 이 메서드를 통해 들어오는 요청을 처리할 컨트롤러를 찾고, 그에 따라 비즈니스 로직을 실행한다.
- **소멸 (Destruction)**: 애플리케이션이 종료되거나 서블릿이 더 이상 필요하지 않으면, `destroy()` 메서드가 호출된다. 이때 서블릿 인스턴스가 소멸되고, 리소스가 해제된다.

```java
import org.springframework.web.servlet.HttpServletBean;

public class CustomDispatcherServlet extends HttpServletBean {
    @Override
    public void init() {
        System.out.println("DispatcherServlet Initialized");
        // 초기화 로직 작성
    }

    @Override
    public void destroy() {
        System.out.println("DispatcherServlet Destroyed");
        // 자원 해제 로직 작성
    }
}
```

## Key Objects related to Servlet

### `HttpServletRequest` & `HttpServletResponse`

`HttpServletRequest`와 `HttpServletResponse`는 **HTTP 요청과 응답을 표현**하는 객체이다.

- **HttpServletRequest**: 클라이언트가 서버로 전송한 HTTP 요청을 나타낸다. 이 객체를 통해 클라이언트가 보낸 파라미터, 헤더, 세션 정보 등을 읽을 수 있다.
- **HttpServletResponse**: 서버가 클라이언트에게 전송할 HTTP 응답을 나타낸다. 이 객체를 사용하여 응답의 상태 코드, 헤더, 본문 등을 설정할 수 있다.

스프링 MVC에서는 이 두 객체를 직접 사용할 수도 있지만, 일반적으로는 스프링이 제공하는 추상화된 방식(`@RequestParam`, `@ModelAttribute` 등)을 통해 더 간편하게 데이터를 주고받는다.

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Controller
public class MyController {

    @GetMapping("/example")
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) {
        String param = request.getParameter("param");
        try {
            response.getWriter().write("Received parameter: " + param);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### `ModelAndView`

`ModelAndView`는 스프링 MVC에서 **컨트롤러가 처리 결과를 뷰에 전달할 때 사용**하는 객체이다. 이 객체는 다음과 같이 두 가지 주요 역할을 수행한다.

- **Model**: 뷰에 전달할 데이터를 담는다. 이는 맵 형태로 저장되며, 뷰에서 이 데이터를 사용하여 결과를 렌더링할 수 있다.
- **View**: 결과를 보여줄 뷰의 이름을 나타낸다. `ViewResolver`가 이 이름을 바탕으로 실제 뷰 파일을 찾아 렌더링한다.

컨트롤러 메서드는 `ModelAndView` 객체를 반환하여, 특정 뷰로 데이터를 전달하고 그 뷰를 렌더링하도록 지시할 수 있다.

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class MyController {

    @GetMapping("/welcome")
    public ModelAndView welcome() {
        ModelAndView mav = new ModelAndView();
        mav.setViewName("welcome");  // 뷰 이름 설정
        mav.addObject("message", "Welcome to Spring MVC!");  // 모델 데이터 설정
        return mav;
    }
}
```

### `HandlerMapping`

`HandlerMapping`은 **들어오는 요청을 적절한 컨트롤러에 매핑**하는 역할을 한다. 스프링 MVC는 여러 종류의 `HandlerMapping`을 제공하며, 요청 URL, HTTP 메서드, 파라미터 등에 따라 적절한 컨트롤러 메서드를 찾아 호출한다. 예를 들어, `@RequestMapping` 어노테이션을 사용하여 특정 URL 패턴에 컨트롤러 메서드를 매핑할 수 있다. `HandlerMapping`은 이 매핑 정보를 바탕으로 요청을 적절한 메서드로 라우팅한다.

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/products")
public class ProductController {

    @GetMapping("/list")
    public String listProducts() {
        // 제품 목록을 반환하는 로직
        return "productList";  // "productList" 뷰를 반환
    }
}
```

### `ViewResolver`

`ViewResolver`는 **컨트롤러에서 반환된 뷰 이름을 실제 뷰로 변환**하는 역할을 한다. 예를 들어, 컨트롤러가 "home"이라는 뷰 이름을 반환하면, `ViewResolver`는 이 이름을 JSP 파일, Thymeleaf 템플릿, 혹은 다른 뷰로 매핑하여 렌더링한다. 스프링은 다양한 `ViewResolver` 구현체를 제공하며, 개발자는 JSP, Thymeleaf, FreeMarker 등 원하는 뷰 기술을 쉽게 사용할 수 있다.

## Flow

스프링 MVC에서의 전체적인 요청 처리 흐름은 다음과 같다.

1. **요청 수신**: 클라이언트가 웹 애플리케이션에 요청을 보내면, 이 요청은 먼저 `DispatcherServlet`에 의해 수신된다.
2. **핸들러 매핑**: `DispatcherServlet`은 요청 URL, HTTP 메서드 등을 바탕으로 `HandlerMapping`을 사용하여 해당 요청을 처리할 컨트롤러 메서드를 찾는다.
3. **컨트롤러 호출**: 찾은 컨트롤러 메서드를 호출하여 요청을 처리하고, 그 결과를 생성한다. 이때, 컨트롤러 메서드는 데이터를 `ModelAndView` 객체에 담아 반환할 수 있다.
4. **뷰 처리**: `DispatcherServlet`은 `ModelAndView`에서 반환된 뷰 이름을 `ViewResolver`를 사용하여 실제 뷰로 변환한다. 이 뷰는 클라이언트에게 보여질 최종 HTML 페이지나 다른 형식의 응답이 된다.
5. **응답 전송**: 최종적으로 렌더링된 뷰를 포함한 응답이 `HttpServletResponse`를 통해 클라이언트에게 전송된다.