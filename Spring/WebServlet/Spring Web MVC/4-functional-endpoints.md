# Functional Endpoints

Spring Web MVC includes WebMvc.fn, a lightweight functional programming model in which functions are used to route and handle requests and contracts are designed for immutability. It is an alternative to the annotation-based programming model but otherwise runs on the same DispatcherServlet.

> Spring Web MVC의 WebMvc.fn은 함수를 사용하여 요청을 라우팅하고 처리하는 경량 함수형 프로그래밍 모델입니다. 이 모델에서는 불변성을 위해 계약(Contract)이 설계되며, 기존의 어노테이션 기반 프로그래밍 모델과 동일한 DispatcherServlet에서 실행됩니다

## Overview

In WebMvc.fn, an HTTP request is handled with a `HandlerFunction`: a function that takes `ServerRequest` and returns a `ServerResponse`. Both the request and the response object have immutable contracts that offer JDK 8-friendly access to the HTTP request and response. `HandlerFunction` is the equivalent of the body of a `@RequestMapping` method in the annotation-based programming model.

> WebMvc.fn에서 HTTP 요청은 `ServerRequest`를 받아 `ServerResponse`를 반환하는 함수인 `HandlerFunction`으로 처리됩니다. 요청과 응답 객체 모두 HTTP 요청과 응답에 대한 JDK 8에서 사용하기 쉬운 변경 불가능한 contracts를 갖습니다. `HandlerFunction`은 어노테이션 기반 프로그래밍 모델에서 `@RequestMapping` 메서드의 본문과 동일한 역할을 합니다.

Incoming requests are routed to a handler function with a `RouterFunction`: a function that takes `ServerRequest` and returns an optional `HandlerFunction` (i.e. `Optional<HandlerFunction>`). When the router function matches, a handler function is returned; otherwise an empty Optional. `RouterFunction` is the equivalent of a `@RequestMapping` annotation, but with the major difference that router functions provide not just data, but also behavior.

> 들어오는 요청은 라우터 함수(`ServerRequest`를 받아 선택적 `HandlerFunction`(예: `Optional<HandlerFunction>`)을 반환하는 함수)가 있는 핸들러 함수로 라우팅됩니다. 라우터 함수가 일치하면 핸들러 함수가 반환되고, 그렇지 않으면 빈 Optional이 반환됩니다. 라우터 함수는 `@RequestMapping` 어노테이션과 동일하지만 데이터뿐만 아니라 동작도 제공한다는 점에서 큰 차이점을 갖습니다.

`RouterFunctions.route()` provides a router builder that facilitates the creation of routers, as the following example shows:

> 라우터 함수 `RouterFunctions.route()`는 다음 예제에서 볼 수 있듯, 라우터 생성을 용이하게 하는 라우터 빌더를 제공합니다:

``` java
import static org.springframework.http.MediaType.APPLICATION_JSON;
import static org.springframework.web.servlet.function.RequestPredicates.*;
import static org.springframework.web.servlet.function.RouterFunctions.route;

PersonRepository repository = ...
PersonHandler handler = new PersonHandler(repository);

RouterFunction<ServerResponse> route = route() (1)
	.GET("/person/{id}", accept(APPLICATION_JSON), handler::getPerson)
	.GET("/person", accept(APPLICATION_JSON), handler::listPeople)
	.POST("/person", handler::createPerson)
	.build();


public class PersonHandler {

	// ...

	public ServerResponse listPeople(ServerRequest request) {
		// ...
	}

	public ServerResponse createPerson(ServerRequest request) {
		// ...
	}

	public ServerResponse getPerson(ServerRequest request) {
		// ...
	}
}
```

(1) Create router using route().

If you register the `RouterFunction` as a bean, for instance by exposing it in a `@Configuration` class, it will be auto-detected by the servlet, as explained in Running a Server.

> 만약 `@Configuration` 클래스에 `RouterFunction`을 빈으로 등록했다면, Running a Server에 설명된 대로 서블릿에 의해 자동으로 감지됩니다.

## HandlerFunction

`ServerRequest` and `ServerResponse` are immutable interfaces that offer JDK 8-friendly access to the HTTP request and response, including headers, body, method, and status code.

> `ServerRequest`와 `ServerResponse`는 헤더, 본문, 메서드 및 상태 코드를 포함하여 HTTP 요청 및 응답에 대한 JDK 8 친화적인 액세스를 제공하는 불변 인터페이스입니다.

### ServerRequest

`ServerRequest` provides access to the HTTP method, URI, headers, and query parameters, while access to the body is provided through the `body` methods.

> `ServerRequest`는 HTTP 메서드, URI, 헤더 및 쿼리 매개변수에 대한 액세스를 제공하며, 본문 메서드를 통해 본문에 대한 접근을 제공합니다.

The following example extracts the request body to a `String`:

> 다음은 요청 본문을 `String`으로 추출하는 예시입니다.

``` java
String string = request.body(String.class);
```

The following example extracts the body to a `List<Person>`, where `Person` objects are decoded from a serialized form, such as JSON or XML:

> 다음은 JSON 또는 XML과 같은 직렬화된 형식에서 Person 개체로 디코딩하여 List<Person>으로 본문을 추출하는 예시입니다:

``` java
List<Person> people = request.body(new ParameterizedTypeReference<List<Person>>() {});
```

The following example shows how to access parameters:

> 다음은 파라미터에 어떻게 접근하는지 보여주는 예시입니다.

``` java
MultiValueMap<String, String> params = request.params();
```

### ServerResponse

`ServerResponse` provides access to the HTTP response and, since it is immutable, you can use a `build` method to create it. You can use the builder to set the response status, to add response headers, or to provide a body. The following example creates a 200 (OK) response with JSON content:

> `ServerResponse`는 HTTP 응답에 대한 액세스를 제공하며, 불변이므로 `build` 메서드를 사용하여 응답을 생성할 수 있습니다. 빌더를 사용하여 응답 상태를 설정하거나 응답 헤더를 추가하거나 본문을 제공할 수 있습니다. 다음은 JSON 콘텐츠가 포함된 200(OK) 응답을 만드는 예시입니다:

``` java
Person person = ...
ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(person);
```

The following example shows how to build a 201 (CREATED) response with a `Location` header and no body:

> 다음 예시는 본문이 없고 `Location` 헤더가 있는 201(CREATED) 응답을 작성하는 방법입니다.:

``` java
URI location = ...
ServerResponse.created(location).build();
```

You can also use an asynchronous result as the body, in the form of a `CompletableFuture`, `Publisher`, or any other type supported by the `ReactiveAdapterRegistry`. For instance:

> 비동기 결과를 `CompletableFuture`, `Publisher` 또는 `ReactiveAdapterRegistry`에서 지원하는 기타 유형의 본문으로 사용할 수도 있습니다. 예를 들어:

``` java
Mono<Person> person = webClient.get().retrieve().bodyToMono(Person.class);
ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(person);
```

If not just the body, but also the status or headers are based on an asynchronous type, you can use the static `async` method on `ServerResponse`, which accepts `CompletableFuture<ServerResponse>`, `Publisher<ServerResponse>`, or any other asynchronous type supported by the `ReactiveAdapterRegistry`. For instance:

> 본문뿐만 아니라 상태 또는 헤더가 비동기 유형을 기반으로 하는 경우, `ServerResponse`에서 정적 비동기 메서드를 사용할 수 있으며, 이 메서드는 `CompletableFuture<ServerResponse>`, `Publisher<ServerResponse>` 또는 `ReactiveAdapterRegistry`에서 지원하는 기타 비동기 유형을 허용합니다. 예를 들어:

``` java
Mono<ServerResponse> asyncResponse = webClient.get().retrieve().bodyToMono(Person.class)
  .map(p -> ServerResponse.ok().header("Name", p.name()).body(p));
ServerResponse.async(asyncResponse);
```

Server-Sent Events can be provided via the static `sse` method on `ServerResponse`. The builder provided by that method allows you to send Strings, or other objects as JSON. For example:

> 서버에서 보낸 이벤트는 `ServerResponse`의 정적 `sse` 메서드를 통해 제공할 수 있습니다. 이 메서드에서 제공하는 빌더를 사용하면 문자열 또는 기타 객체를 JSON으로 전송할 수 있습니다. 예를 들면:

``` java
public RouterFunction<ServerResponse> sse() {
	return route(GET("/sse"), request -> ServerResponse.sse(sseBuilder -> {
				// Save the sseBuilder object somewhere..
			}));
}

// In some other thread, sending a String
sseBuilder.send("Hello world");

// Or an object, which will be transformed into JSON
Person person = ...
sseBuilder.send(person);

// Customize the event by using the other methods
sseBuilder.id("42")
		.event("sse event")
		.data(person);

// and done at some point
sseBuilder.complete();
```

> 지금까지 글을 요약하자면, Spring Web MVC의 WebMvc.fn은 함수형 프로그래밍 모델을 사용하여 경량화된 HTTP 요청 처리 방식을 제공합니다. HandlerFunction과 RouterFunction을 사용하여 요청을 처리하고 라우팅할 수 있으며, ServerRequest와 ServerResponse를 통해 HTTP 요청 및 응답에 대한 액세스를 제공합니다.

### Handler Classes

We can write a handler function as a lambda, as the following example shows:

> 우리는 다음과 같이 람다를 사용하여 handler 함수를 작성할 수 있습니다:

``` java
HandlerFunction<ServerResponse> helloWorld =
  request -> ServerResponse.ok().body("Hello World");
```

That is convenient, but in an application we need multiple functions, and multiple inline lambda’s can get messy. Therefore, it is useful to group related handler functions together into a handler class, which has a similar role as `@Controller` in an annotation-based application. For example, the following class exposes a reactive `Person` repository:

> 이것은 편리하지만 여러 함수가 필요한 애플리케이션에서 인라인 람다 표현식을 여러 개 사용하면 지저분해질 수 있습니다. 따라서 handler 함수를 클래스로 그룹화하는 것이 좋습니다. 해당 클래스는 어노테이션 기반 애플리케이션에서의 `@Controller`와 비슷한 역할을 합니다. 예를 들어 다음 클래스는 반응형 `Person` repositoriy를 보여줍니다:

``` java
import static org.springframework.http.MediaType.APPLICATION_JSON;
import static org.springframework.web.reactive.function.server.ServerResponse.ok;

public class PersonHandler {

	private final PersonRepository repository;

	public PersonHandler(PersonRepository repository) {
		this.repository = repository;
	}

	public ServerResponse listPeople(ServerRequest request) { (1)
		List<Person> people = repository.allPeople();
		return ok().contentType(APPLICATION_JSON).body(people);
	}

	public ServerResponse createPerson(ServerRequest request) throws Exception { (2)
		Person person = request.body(Person.class);
		repository.savePerson(person);
		return ok().build();
	}

	public ServerResponse getPerson(ServerRequest request) { (3)
		int personId = Integer.parseInt(request.pathVariable("id"));
		Person person = repository.getPerson(personId);
		if (person != null) {
			return ok().contentType(APPLICATION_JSON).body(person);
		}
		else {
			return ServerResponse.notFound().build();
		}
	}

}
```

1. listPeople is a handler function that returns all Person objects found in the repository as JSON.
2. createPerson is a handler function that stores a new Person contained in the request body.
3. getPerson is a handler function that returns a single person, identified by the id path variable. We retrieve that Person from the repository and create a JSON response, if it is found. If it is not found, we return a 404 Not Found response.

> 1. listPeople은 리포지토리에 있는 모든 Person 객체를 JSON으로 반환하는 handler 함수입니다.
> 2. createPerson은 요청 본문에 포함된 새 Person을 저장하는 handler 함수입니다.
> 3. getPerson은 id 경로 변수로 식별되는 단일 Person 객체를 반환하는 핸들러 함수입니다. 리포지토리에서 해당 사람을 검색하여 찾을 수 있는 경우, JSON 응답을 생성합니다. 찾을 수 없으면 404 Not Found 응답을 반환합니다.

### Validation

A functional endpoint can use Spring’s validation facilities to apply validation to the request body. For example, given a custom Spring Validator implementation for a Person:

> 함수형 엔드포인트는 Spring의 유효성 검사 기능을 사용하여 요청 본문에 유효성 검사를 적용할 수 있습니다. 예를 들어, Person에 대한 사용자 정의 Spring 유효성 검사기 구현이 있다고 가정해 보겠습니다:

``` java
public class PersonHandler {

	private final Validator validator = new PersonValidator(); (1)

	// ...

	public ServerResponse createPerson(ServerRequest request) {
		Person person = request.body(Person.class);
		validate(person); (2)
		repository.savePerson(person);
		return ok().build();
	}

	private void validate(Person person) {
		Errors errors = new BeanPropertyBindingResult(person, "person");
		validator.validate(person, errors);
		if (errors.hasErrors()) {
			throw new ServerWebInputException(errors.toString()); (3)
		}
	}
}
```

1. Create Validator instance.
2. Apply validation.
3. Raise exception for a 400 response.

> 1. Validator 인스턴스를 생성합니다.
> 2. 유효성 검사를 적용합니다.
> 3. 400 응답에 대한 예외를 발생시킵니다.

Handlers can also use the standard bean validation API (JSR-303) by creating and injecting a global `Validator` instance based on `LocalValidatorFactoryBean`.

> 또한 핸들러는 `LocalValidatorFactoryBean`을 기반으로 한 전역 `Validator` 인스턴스를 생성하고 주입하여 표준 빈 유효성 검사 API를 사용할 수 있습니다.

## RouterFunction

### Predicates

### Routes

### Nested Routes

## Running a Server

## Filtering Handler Functions
