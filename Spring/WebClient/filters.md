## Filters

You can register a client filter (`ExchangeFilterFunction`) through the `WebClient.Builder` in order to intercept and modify requests, as the following example shows:

> 다음 예제와 같이, 요청을 변경하거나 가로채기 위해 `WebClient.Builder`를 사용하여 클라이언트 필터(`ExchangeFilterFunction`)를 등록할 수 있습니다.

``` java
WebClient client = WebClient.builder()
		.filter((request, next) -> {

			ClientRequest filtered = ClientRequest.from(request)
					.header("foo", "bar")
					.build();

			return next.exchange(filtered);
		})
		.build();
```

This can be used for cross-cutting concerns, such as authentication. The following example uses a filter for basic authentication through a static factory method:

> 필터는 인증과 같은 교차 문제에 사용할 수 있습니다. 다음의 예제는 정적 팩토리 메서드를 통한 기본 인증 필터를 사용하고 있습니다.

``` java
import static org.springframework.web.reactive.function.client.ExchangeFilterFunctions.basicAuthentication;

WebClient client = WebClient.builder()
		.filter(basicAuthentication("user", "password"))
		.build();
```

Filters can be added or removed by mutating an existing `WebClient` instance, resulting in a new `WebClient` instance that does not affect the original one. For example:

> 필터는 기존 `WebClient` 인스턴스를 mutate하여 추가하거나 제거할 수 있으며, 그 결과 기존 `WebClient`에 영향을 주지 않는 새 `WebClient` 인스턴스가 생성됩니다. 예를 들어:

``` java
import static org.springframework.web.reactive.function.client.ExchangeFilterFunctions.basicAuthentication;

WebClient client = webClient.mutate()
		.filters(filterList -> {
			filterList.add(0, basicAuthentication("user", "password"));
		})
		.build();
```

`WebClient` is a thin facade around the chain of filters followed by an `ExchangeFunction`. It provides a workflow to make requests, to encode to and from higher level objects, and it helps to ensure that response content is always consumed. When filters handle the response in some way, extra care must be taken to always consume its content or to otherwise propagate it downstream to the `WebClient` which will ensure the same. Below is a filter that handles the `UNAUTHORIZED` status code but ensures that any response content, whether expected or not, is released:

> `WebClient`는 일련의 필터 체인을 둘러싸고 있는 얇은 파사드이며, 그 뒤에 `ExchangeFunction`이 있습니다. 상위 수준의 객체로부터 요청을 만들고 인코딩하는 워크플로우를 제공하며, 응답 컨텐츠가 항상 소비되도록 하는 데 도움을 줍니다. 필터가 어떤 방식으로든 응답을 처리할 때는, 항상 컨텐츠를 소비하거나 다운 스트림으로 전달하여 동일한 결과를 보장할 수 있도록 각별한 주의를 기울여야 합니다. 아래 예제는 미승인 상태 코드를 처리하지만 예상 여부와 관계없이 모든 응답 컨텐츠가 릴리즈되도록 하는 필터입니다:

``` java
public ExchangeFilterFunction renewTokenFilter() {
	return (request, next) -> next.exchange(request).flatMap(response -> {
		if (response.statusCode().value() == HttpStatus.UNAUTHORIZED.value()) {
			return response.releaseBody()
					.then(renewToken())
					.flatMap(token -> {
						ClientRequest newRequest = ClientRequest.from(request).build();
						return next.exchange(newRequest);
					});
		} else {
			return Mono.just(response);
		}
	});
}
```

- `renewTokenFilter()`는 `ExchangeFilterFunction`을 반환합니다. 이는 WebFlux의 Exchange 함수 체인에서 동작하는 필터를 표현합니다.
- renewTokenFilter 필터는 주어진 요청(request)과 다음 필터 또는 Exchange 함수(next)를 이용하여 요청을 수행합니다.
- `next.exchange(request)`는 현재 요청을 실행하고 그 결과로 나오는 response를 얻습니다.
- 만약 응답(response)의 상태 코드가 `UNAUTHORIZED` 상태 코드인 경우, (메모리 누수 방지를 위해) response.releaseBody()를 통해 응답의 내용을 해제합니다. 그런 다음 `renewToken()` 함수를 호출합니다. 이 함수는 토큰을 갱신하고, 갱신된 토큰을 포함한 새로운 요청(newRequest)을 생성합니다. 마지막으로, `next.exchange(newRequest)`을 호출하여 새로 갱신된 토큰을 사용한 요청을 다시 실행합니다.
- 응답의 상태 코드가 `UNAUTHORIZED`가 아닌 경우, 현재 응답을 그대로 반환합니다.
