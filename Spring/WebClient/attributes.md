## Attributes

You can add attributes to a request. This is convenient if you want to pass information through the filter chain and influence the behavior of filters for a given request. For example:

> 요청에 속성을 추가할 수 있습니다. 이 기능은 필터 체인을 통해 정보를 전달하고 특정 요청에 대한 필터의 동작에 영향을 미치려는 경우에 편리합니다. 예를 들어:

``` java
WebClient client = WebClient.builder()
		.filter((request, next) -> {
			Optional<Object> usr = request.attribute("myAttribute");
			// ...
		})
		.build();

client.get().uri("https://example.org/")
		.attribute("myAttribute", "...")
		.retrieve()
		.bodyToMono(Void.class);
```

Note that you can configure a `defaultRequest` callback globally at the `WebClient.Builder` level which lets you insert attributes into all requests, which could be used for example in a Spring MVC application to populate request attributes based on `ThreadLocal` data.

> `WebClient.Builder` 수준에서 `defaultRequest` 콜백을 구성할 수 있습니다. 이를 통해 모든 요청에 속성을 추가할 수 있으며, 예를 들어 `ThreadLocal` 데이터를 기반으로 한 Spring MVC 응용 프로그램에서 요청 속성을 자동으로 채우는 데 사용될 수 있습니다.
