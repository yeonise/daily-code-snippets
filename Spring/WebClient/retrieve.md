## retrieve()

The `retrieve()` method can be used to declare how to extract the response. For example:

> `retrieve()` 메서드는 응답을 어떻게 추출할지 결정하는데 사용합니다. 예를 들면:

``` Java
WebClient client = WebClient.create("https://example.org");

Mono<ResponseEntity<Person>> result = client.get()
		.uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
		.retrieve()
		.toEntity(Person.class);
```

Or to get only the body:

> 또는 body만 추출할 수도 있습니다:

``` Java
WebClient client = WebClient.create("https://example.org");

Mono<Person> result = client.get()
		.uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
		.retrieve()
		.bodyToMono(Person.class);
```

To get a stream of decoded objects:

> 디코딩된 오브젝트의 스트림을 가져올 수도 있습니다:

``` Java
Flux<Quote> result = client.get()
		.uri("/quotes").accept(MediaType.TEXT_EVENT_STREAM)
		.retrieve()
		.bodyToFlux(Quote.class);
```

By default, 4xx or 5xx responses result in an `WebClientResponseException`, including sub-classes for specific HTTP status codes. To customize the handling of error responses, use `onStatus` handlers as follows:

> 기본적으로, 400번대 또는 500번대 응답은 특정 HTTP 상태 코드에 대한 하위 클래스를 포함한 `WebClientResponseException`를 발생시킵니다. 다음과 같이 에러 응답을 사용자 정의하기 위해서 `onStatus`를 사용할 수도 있습니다:

``` Java
Mono<Person> result = client.get()
		.uri("/persons/{id}", id).accept(MediaType.APPLICATION_JSON)
		.retrieve()
		.onStatus(HttpStatus::is4xxClientError, response -> ...)
		.onStatus(HttpStatus::is5xxServerError, response -> ...)
		.bodyToMono(Person.class);
```

```
memo

- SpringWebFlux를 사용하여 비동기적인 데이터 스트림 처리를 Reactor 라이브러리가 제공하는 데이터 타입인 Mono와 Flux로 다뤄야 한다.
- 즉, WebFlux에서는 모든 응답을 Mono 혹은 Flux에 담아서 반환해야 한다.

- Mono는 0-1개의 결과만을 처리하기 위한 Reactor 객체, Flux는 0-N개의 결과를 처리하는 객체.
- 예를 들어 Flux에서 하나의 결과로 값을 모아주는 reduce 연산자는 Mono를 반환하고, Mono에서 flatMapMany 연산자를 사용하면 Flux를 반환한다.
- 즉, 단일 항목의 경우 bodyToMono, 여러 항목의 경우 bodyToFlux.
```
