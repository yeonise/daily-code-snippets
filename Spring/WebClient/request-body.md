## Request Body

The request body can be encoded from any asynchronous type handled by `ReactiveAdapterRegistry`, like `Mono` or Kotlin Coroutines `Deferred` as the following example shows:

> 요청 본문은 다음 예제에서 볼 수 있듯이 `Mono` 또는 Kotlin 코루틴 `Deferred`와 같이 `ReactiveAdapterRegistry`에서 처리하는 모든 비동기 유형으로 인코딩할 수 있습니다:

``` java
Mono<Person> personMono = ... ; // Person 객체를 갖는 Mono를 생성

Mono<Void> result = client.post() // HTTP 요청 생성
		.uri("/persons/{id}", id)
		.contentType(MediaType.APPLICATION_JSON) // 요청 컨텐츠 타입 지정
		.body(personMono, Person.class) // 요청 본문 설정
		.retrieve() // 요청을 보내고 응답을 받아오기 위한 메서드
		.bodyToMono(Void.class); // 응답 본문을 Mono<Void>로 변환, 즉 응답 본문을 처리하지 않고 상태만 살펴보겠다는 의미
```

You can also have a stream of objects be encoded, as the following example shows:

> 다음 예제에서 볼 수 있듯이 오브젝트 스트림을 인코딩하도록 할 수도 있습니다:

``` java
Flux<Person> personFlux = ... ; // 여러 개의 요소를 비동기적으로 처리할 수 있는 리액티브 타입

Mono<Void> result = client.post()
		.uri("/persons/{id}", id)
		.contentType(MediaType.APPLICATION_STREAM_JSON) // JSON 스트림 형식으로 데이터를 처리
		.body(personFlux, Person.class)
		.retrieve()
		.bodyToMono(Void.class);
```

Alternatively, if you have the actual value, you can use the `bodyValue` shortcut method, as the following example shows:

> 또는 실제 값이 있는 경우 다음 예시처럼 `bodyValue` 바로 가기 메서드를 사용할 수 있습니다:

``` java
Person person = ... ; // 일반적인 동기적 프로그래밍

Mono<Void> result = client.post()
		.uri("/persons/{id}", id)
		.contentType(MediaType.APPLICATION_JSON)
		.bodyValue(person) // 단일 객체 전송
		.retrieve()
		.bodyToMono(Void.class);
```

### Form Data

To send form data, you can provide a `MultiValueMap<String, String>` as the body. Note that the content is automatically set to `application/x-www-form-urlencoded` by the `FormHttpMessageWriter`. The following example shows how to use `MultiValueMap<String, String>`:

> 양식 데이터를 전송하려면 `MultiValueMap<String, String>`을 본문으로 제공할 수 있습니다. 콘텐츠는 `FormHttpMessageWriter`에 의해 자동으로 `application/x-www-form-urlencoded`로 설정된다는 점에 유의하세요. 다음 예제는 `MultiValueMap<String, String>`을 사용하는 방법을 보여줍니다:

``` java
MultiValueMap<String, String> formData = ... ;

Mono<Void> result = client.post()
		.uri("/path", id)
		.bodyValue(formData)
		.retrieve()
		.bodyToMono(Void.class);
```

You can also supply form data in-line by using `BodyInserters`, as the following example shows:

> 다음 예제에서 볼 수 있듯이 `BodyInserters`를 사용하여 양식 데이터를 인라인으로 제공할 수도 있습니다:

``` java
import static org.springframework.web.reactive.function.BodyInserters.*;

Mono<Void> result = client.post()
		.uri("/path", id)
		.body(fromFormData("k1", "v1").with("k2", "v2"))
		.retrieve()
		.bodyToMono(Void.class);
```

### Multipart Data

To send multipart data, you need to provide a `MultiValueMap<String, ?>` whose values are either `Object` instances that represent part content or `HttpEntity` instances that represent the content and headers for a part. `MultipartBodyBuilder` provides a convenient API to prepare a multipart request. The following example shows how to create a `MultiValueMap<String, ?>`:

> multipart data를 전송하려면 part 콘텐츠를 나타내는 `Object` 인스턴스 또는 part의 콘텐츠와 헤더를 나타내는 `HttpEntity` 인스턴스를 값으로 하는 `MultiValueMap<String, ?>`을 제공해야 합니다. `MultipartBodyBuilder`는 multipart 요청을 준비하기 위한 편리한 API를 제공합니다. 다음 예제는 `MultiValueMap<String, ?>`을 생성하는 방법을 보여줍니다:

``` java
MultipartBodyBuilder builder = new MultipartBodyBuilder();
builder.part("fieldPart", "fieldValue");
builder.part("filePart1", new FileSystemResource("...logo.png"));
builder.part("jsonPart", new Person("Jason"));
builder.part("myPart", part); // Part from a server request

MultiValueMap<String, HttpEntity<?>> parts = builder.build();
```

In most cases, you do not have to specify the `Content-Type` for each part. The content type is determined automatically based on the `HttpMessageWriter` chosen to serialize it or, in the case of a `Resource`, based on the file extension. If necessary, you can explicitly provide the `MediaType` to use for each part through one of the overloaded builder `part` methods.

> 대부분의 경우 각 부분에 대해 `Content-Type`을 지정하지 않아도 됩니다. 콘텐츠 유형은 직렬화하기 위해 선택한 `HttpMessageWriter`에 따라 또는 리소스의 경우 파일 확장자에 따라 자동으로 결정됩니다. 필요한 경우 오버로드된 빌더 파트 메서드 중 하나를 통해 각 파트에 사용할 MediaType을 명시적으로 제공할 수 있습니다.

Once a `MultiValueMap` is prepared, the easiest way to pass it to the `WebClient` is through the `body` method, as the following example shows:

> `MultiValueMap`이 준비되면 `WebClient`에 전달하는 가장 쉬운 방법은 `body` 메서드를 사용하는 것입니다:

``` java
MultipartBodyBuilder builder = ...;

Mono<Void> result = client.post()
		.uri("/path", id)
		.body(builder.build())
		.retrieve()
		.bodyToMono(Void.class);
```

If the `MultiValueMap` contains at least one non-`String` value, which could also represent regular form data (that is, `application/x-www-form-urlencoded`), you need not set the `Content-Type` to `multipart/form-data`. This is always the case when using `MultipartBodyBuilder`, which ensures an `HttpEntity` wrapper.

> `MultiValueMap`에 일반 양식 데이터(즉, application/x-www-form-urlencoded)를 나타낼 수 있는 문자열이 아닌 값이 적어도 하나 이상 포함된 경우 Content-Type을 multipart/form-data로 설정하지 않아도 됩니다. 이는 `HttpEntity` 래퍼를 보장하는 `MultipartBodyBuilder`를 사용할 때에도 자동으로 설정됩니다.

As an alternative to `MultipartBodyBuilder`, you can also provide multipart content, inline-style, through the built-in `BodyInserters`, as the following example shows:

> `MultipartBodyBuilder` 대신 다음 예시처럼 내장된 `BodyInserters`를 통해 인라인 스타일로 멀티파트 콘텐츠를 제공할 수도 있습니다:

``` java
import static org.springframework.web.reactive.function.BodyInserters.*;

Mono<Void> result = client.post()
		.uri("/path", id)
		.body(fromMultipartData("fieldPart", "value").with("filePart", resource))
		.retrieve()
		.bodyToMono(Void.class);
```

#### PartEvent

To stream multipart data sequentially, you can provide multipart content through `PartEvent` objects.

- Form fields can be created via `FormPartEvent::create`.
- File uploads can be created via `FilePartEvent::create`.

You can concatenate the streams returned from methods via `Flux::concat`, and create a request for the `WebClient`.

For instance, this sample will POST a multipart form containing a form field and a file.

``` java
Resource resource = ...
Mono<String> result = webClient
    .post()
    .uri("https://example.com")
    .body(Flux.concat(
            FormPartEvent.create("field", "field value"),
            FilePartEvent.create("file", resource)
    ), PartEvent.class)
    .retrieve()
    .bodyToMono(String.class);
```

On the server side, `PartEvent` objects that are received via `@RequestBody` or `ServerRequest::bodyToFlux(PartEvent.class)` can be relayed to another service via the `WebClient`.
