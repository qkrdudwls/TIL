# Mapping Annotation

스프링에서 매핑 어노테이션은 **클라이언트의 HTTP 요청을 통해 특정 메서드와 연결**하는 데 사용된다. 이 어노테이션들은 각각의 목적에 맞게 HTTP 메서드(GET, POST, PUT, DELETE 등), URL 경로, 요청 파라미터, 헤더 등을 세밀하게 설정할 수 있다. 

## `@RequestMapping`

`@RequestMapping`은 가장 범용적인 어노테이션으로, **URL 경로와 HTTP 메서드를 매핑**한다. 클래스 또는 메서드에 적용될 수 있으며, 다양한 속성을 통해 요청을 처리하는 방식을 세부적으로 정의할 수 있다.

### Attributes

- **value** 또는 **path**: 매핑할 URL 경로를 지정한다. 배열로 여려 경로를 지정할 수 있다.
- **method**: 매핑할 HTTP 메서드를 지정한다. 예를 들어, `RequestMethod.GET` , `RequestMethod.POST` 등을 사용할 수 있다.
- **params**: 특정 요청 파라미터가 존재하거나 특정 값이어야 하는 경우, 이를 매핑 조건으로 사용할 수 있다.
- **headers**: 특정 HTTP 헤더가 존재하거나 특정 값이어야 하는 경우, 이를 매핑 조건으로 사용할 수 있다.
- **consumes**: 요청의 Content-Type을 지정한다. 예를 들어, `application/json` 같은 값을 사용할 수 있다.
- **produces**: 응답의 Content-Type을 지정한다. 예를 들어, `application/json`  또는 `text/html` 등을 지정할 수 있다.

## `@GetMapping` , `@PostMapping` , `@PutMapping` , `DeleteMapping` , `@PatchMapping`

스프링 4.3부터 도입된 이 어노테이션들은 `@RequestMapping` 의 **단순화**된 버전이다. 각 어노테이션은 특정 HTTP 메서드와의 매핑을 더욱 직관적으로 표현할 수 있게 한다. 

### Attributes

- **value** 또는 **path**: 매핑할 URL 경로를 지정한다.
- **params**: 특정 요청 파라미터가 존재하거나 특정 값이어야 하는 경우 이를 매핑 조건으로 사용할 수 있다.
- **headers**: 특정 HTTP 헤더가 존재하거나 특정 값이어야 하는 경우 이를 매핑 조건으로 사용할 수 있다.
- **consumes**: 요청의 Content-Type을 지정한다.
- **produces**: 응답의 Content-Type을 지정한다.

## `@PathVariable`

`@PathVariable` 은 **URL 경로의 일부를 변수로 받아 메서드 파라미터로 사용할 때 사용**된다. RESTful API에서 자주 사용된다. 

### Attributes

- **value**: URL 경로 변수의 이름을 지정한다. 생략하면 메서드 파라미터의 이름과 URL 경로 변수의 이름이 동일해야 한다.
- **required**: 이 속성은 경로 변수가 필수인지 여부를 지정한다. 기본값은 `true이`다.

## `@RequestParam`

`@RequestParam` 은 **URL 쿼리 스트링이나 폼 데이터를 메서드 파라미터에 매핑할 때 사용**된다.

### Attributes

- **value**: 요청 파라미터의 이름을 지정한다. 생략하면 메서드 파라미터의 이름과 요청 파라미터의 이름이 동일해야 한다.
- **required**: 이 속성은 해당 파라미터가 필수인지 여부를 지정한다. 기본값은 `true`이다.
- **defaultValue**: 파라미터가 제공되지 않았을 때 사용할 기본값을 지정한다.

## `@RequestBody`

`@RequestBody` 는 **HTTP 요청의 본문을 메서드 파라미터로 매핑할 때 사용**된다. 주로 JSON이나 XML 같은 구조화된 데이터를 처리할 때 사용된다.

### Attributes

- **required**: 요청 본문이 필수인지 여부를 지정한다. 기본값은 `true` 이다.

## `@RequestHeader`

`@RequestHeader` 는 **HTTP 요청 헤더를 메서드 파라미터로 매핑할 때 사용**된다.

### Attributes

- **value**: 헤더 이름을 지정한다.
- **required**: 헤더가 필수인지 여부를 지정한다. 기본값은 `true` 이다.
- **defaultValue**: 헤더가 제공되지 않았을 때 사용할 기본값을 지정한다.

## `@CookieValue`

`@CookieValue` 는 **HTTP 요청의 쿠키 값을 메서드 파라미터로 매핑할 때 사용**된다.

### Attributes

- **value**: 쿠키의 이름을 지정한다.
- **required**: 쿠키가 필수인지 여부를 지정한다. 기본값은 `true` 이다.
- **defaultValue**: 쿠키가 제공되지 않았을 때 사용할 기본값을 지정한다.

## `@ModelAttribute`

`@ModelAttribute` 는 **요청 파라미터를 바인딩하여 객체를 생성하고, 그 객체를 모델에 추가하거나 메서드 파라미터로 전달할 때 사용**된다.

### Attributes

- **value**: 모델에 저장할 이름을 지정한다. 생략하면 클래스 이름이 사용된다.