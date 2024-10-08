# JSP

JSP(Jakarta Server Pages, 이전 명칭: JavaServer Pages)는 자바 기반 웹 애플리케이션을 개발하기 위한 기술 중 하나로, **HTML 문서 내에 자바 코드를 삽입하여 동적인 웹 페이지를 생성**할 수 있도록 한다. JSP는 **웹 서버에서 실행**되며, 클라이언트가 요청한 HTML 페이지를 동적으로 생성하여 반환하는 역할을 한다.

## Features

- **HTML과 자바 코드의 혼합**: JSP 파일은 주로 `.jsp` 확장자를 가지며, HTML 태그와 자바 코드를 함께 작성할 수 있다. 자바 코드는 `<% %>` 태그 안에 작성된다. 예를 들어, `<% out.print("Hello, World!"); %>`와 같이 JSP 페이지에 자바 코드를 삽입할 수 있다.
- **서버 사이드 기술**: JSP는 서버에서 실행되는 서버 사이드 기술이다. 클라이언트가 JSP 페이지를 요청하면 서버는 JSP 페이지를 컴파일하여 서블릿(Servlet)으로 변환하고, 이 서블릿이 실행되어 HTML 페이지를 생성하고 클라이언트에게 응답한다.
- **서블릿과의 관계**: JSP는 본질적으로 서블릿의 상위 개념이다. JSP 페이지는 컴파일 시 서블릿으로 변환되며, 이렇게 변환된 서블릿은 웹 서버에서 실행된다. 이를 통해 JSP는 서블릿의 강력함과 HTML의 간결함을 모두 제공한다.
- **표현 언어(EL)와 JSTL**: JSP에서는 표현 언어(EL)와 JSTL(JavaServer Pages Standard Tag Library)를 사용하여 자바 코드를 더 간결하고 안전하게 표현할 수 있다. EL은 변수나 객체의 속성에 쉽게 접근할 수 있게 해주며, JSTL은 조건문, 반복문 등을 HTML 태그 형태로 사용할 수 있게 한다.

## Relationship between Servlet and JSP

서블릿은 자바 기반 웹 애플리케이션에서 HTTP 요청과 응답을 처리하는 자바 클래스로, 주로 프로그래밍 로직을 포함하며, 클라이언트 요청을 처리하고 응답을 생성하는 역할을 한다. 

### Role of Servlet

서블릿은 HTTP 요청을 받아들이고, 이를 처리하여 HTML이나 JSON 등의 형태로 응답을 생성한다. 서블릿은 `doGet()` , `doPost()` 와 같은 메서드를 통해 GET, POST 요청을 처리하며, 요청의 파라미터를 분석하거나 데이터베이스와 연동하여 동적인 응답을 생성할 수 있다.

### Difference between Servlet and JSP

JSP는 주로 **뷰 계층에서 사용**되며, HTML과 함께 동적인 콘텐츠를 쉽게 생성할 수 있는 방법을 제공한다. 반면, 서블릿은 주로 **비즈니스 로직과 관련된 작업을 처리**하며, **JSP에 데이터를 전달하여 클라이언트에게 보여줄 콘텐츠를 준비**하는 역할을 한다.

### Converting JSP to Servlet

JSP 파일이 실행되면, 이 파일은 **자동으로 서블릿으로 변환**된다. 이 변환된 서블릿은 클라이언트의 요청에 따라 동적으로 HTML 콘텐츠를 생성한다. 따라서 JSP는 서블릿의 상위 레벨에서 보다 쉽게 동적인 웹 페이지를 작성할 수 있는 도구로 볼 수 있다.

## Using JSP and Servlet

JSP와 서블릿은 자바 기반 웹 애플리케이션에서 MVC 패턴을 구현하는 데 자주 사용된다. 서블릿은 **컨트롤러로서 클라이언트의 요청을 받아 모델과 상호 작용**하며, 그 **결과를 JSP에 전달**하여 사용자에게 보여준다.

- **Model**: 애플리케이션의 데이터 및 비즈니스 로직을 처리하는 부분으로, 주로 **Java Beans**나 **EJB**를 사용하여 구현된다.
- **View**: 사용자에게 보여지는 화면으로, **JSP가 이 역할을 수행**한다.
- **Controller**: 사용자의 요청을 받아 처리하고, 그 결과를 뷰로 전달하는 역할을 한다. **서블릿이 이 역할을 수행**한다.

## Flow

클라이언트가 웹 브라우저를 통해 서버에 요청을 보내고, 서버가 해당 요청을 처리하여 응답을 반환하는 과정은 다음과 같다.

### 1. 클라이언트 요청 (HTTP Request)

사용자가 웹 브라우저를 통해 특정 URL을 입력하거나, 웹 페이지에서 링크를 클릭하거나, 폼을 제출하면, 웹 브라우저는 HTTP 요청을 웹 서버로 전송한다. 이 요청은 URL, 요청 메서드 (GET, POST 등), 요청 파라미터 등을 포함한다.

### 2. 웹 서버가 요청을 수신

Apache Tomcat과 같은 웹 서버는 클라이언트로부터 받은 HTTP 요청을 수신한다. 요청된 URL을 분석하여 해당 요청이 JSP 파일에 대한 것인지, 서블릿에 대한 것인지 확인한다.

### 3. 서블릿 컨테이너 처리

만약 요청이 서블릿에 대한 것이라면, 서블릿 컨테이너가 해당 서블릿 클래스를 메모리로 로드하고, 요청을 처리할 수 있도록 인스턴스를 생성하거나, 기존 인스턴스를 사용한다. 만약 요청이 처음이라면, 서블릿 컨테이너가 서블릿의 `init()` 메서드를 호출하여 서블릿을 초기화한다.

### 4. 서블릿의 요청 처리

서블릿이 클라이언트의 요청을 처리하는 과정에서 `doGet()` , `doPost()` 와 같은 메서드를 통해 요청을 처리한다. 이 과정에서 요청 파라미터를 추출하거나, 데이터베이스와 상호 작용하는 등의 작업을 수행할 수 있다. 서블릿은 요청 처리 후 결과 데이터를 JSP에 전달하거나 직접 HTTP 응답을 생성할 수 있다.

### 5. JSP로 포워딩 또는 직접 응답

서블릿은 요청을 처리한 후, 종종 결과를 JSP 페이지로 포워딩하여 뷰를 생성한다. 이는 `RequestDispatcher` 객체를 사용해 이루어진다. 예를 들어, `RequestDispatcher dispatcher = request.getRequestDispatcher("result.jsp");`와 같은 방식으로 서블릿에서 JSP로 요청을 포워딩한다. JSP는 전달받은 데이터를 기반으로 동적인 HTML 페이지를 생성한다.

### 6. JSP 컴파일 및 서블릿으로 변환

클라이언트가 JSP 페이지를 직접 요청하거나 서블릿이 JSP로 요청을 포워딩하면, 웹 서버는 해당 JSP 파일이 이전에 컴파일 된 적이 있는지 확인한다. 만약 JSP가 처음 요청되었거나, 수정된 이후 첫 요청이라면, JSP 파일은 서블릿 코드로 변환된 후 자바 서블릿 클래스로 컴파일된다. 이때, 이 서블릿 클래스는 메모리에 로드되어 실행 가능한 형태로 준비된다.

### 7. JSP 서블릿 실행

JSP가 서블릿으로 변환된 후, 해당 서블릿은 실행되어 클라이언트에게 반환할 HTML 문서를 생성한다. JSP 서블릿은 동적인 내용을 포함한 HTML을 생성하며, 이 과정에서 EL 또는 JSTL을 사용해 자바 코드를 간결하게 작성할 수 있다.

### 8. HTTP 응답

서블릿이나 JSP에서 생성된 최종 HTML 콘텐츠가 클라이언트에게 HTTP 응답으로 전송된다. 이 응답은 클라이언트의 웹 브라우저에서 렌더링되어 사용자가 볼 수 있는 웹 페이지로 나타난다.