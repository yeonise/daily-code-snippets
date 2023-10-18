## Context

Attributes provide a convenient way to pass information to the filter chain but they only influence the current request. If you want to pass information that propagates to additional requests that are nested, e.g. via `flatMap`, or executed after, e.g. via `concatMap`, then you’ll need to use the Reactor `Context`.

> Attributes는 필터 체인에 정보를 전달하는 편리한 방법을 제공하지만 현재 요청에만 영향을 줍니다. flatMap을 통해 중첩되거나 concatMap을 통해 실행되는 추가 요청으로 전파되는 정보를 전달하려면 Reactor `Context`를 사용해야 합니다.

The Reactor `Context` needs to be populated at the end of a reactive chain in order to apply to all operations. For example:

> Reactor `Context`를 모든 작업에 적용하려면 리액티브 체인의 마지막에 채워져야 합니다. 예를 들어:

``` java
WebClient client = WebClient.builder()
		.filter((request, next) ->
				Mono.deferContextual(contextView -> {
					String value = contextView.get("foo");
					// ...
				}))
		.build();

client.get().uri("https://example.org/")
		.retrieve()
		.bodyToMono(String.class)
		.flatMap(body -> {
				// perform nested request (context propagates automatically)...
		})
		.contextWrite(context -> context.put("foo", ...));
```

**예시**

``` java
import org.springframework.web.reactive.function.client.WebClient;
import reactor.core.publisher.Mono;

public class WebClientExample {

    public static void main(String[] args) {
        // WebClient 생성
        WebClient client = WebClient.builder()
                .filter((request, next) ->
                        Mono.deferContextual(contextView -> {
                            // Reactor Context에서 "userToken" 속성을 가져옴
                            String userToken = contextView.get("userToken");
                            System.out.println("User Token: " + userToken);

                            // 다양한 작업 수행 가능, 이 예제에서는 간단히 로그 출력
                            // ...

                            // 다음 필터 또는 요청으로 전파하기 위해 현재 Context 유지
                            return next.exchange(request);
                        }))
                .build();

        // 첫 번째 요청: Reactor Context에 "userToken" 추가
        String initialToken = "abc123";
        Mono<String> response1 = client.get().uri("https://example.org/")
                .retrieve()
                .bodyToMono(String.class)
                .contextWrite(context -> context.put("userToken", initialToken));
        // 결과: "User Token: abc123"이 출력된다.

        // 두 번째 요청: 이전 요청의 Reactor Context가 유지되어 "userToken" 자동 전파
        // 두 번째 요청에서는 이전 요청에서 설정한 "userToken" 속성을 자동으로 가져와 사용할 수 있습니다.
        Mono<String> response2 = response1.flatMap(body -> {
            // 여기서는 단순히 로그 출력을 가정
            System.out.println("Nested Request Body: " + body);
            // 결과: "Nested Request Body: [첫 번째 요청의 응답 본문]"이 출력됩니다.

            // 두 번째 요청에서 새로운 Reactor Context 속성 추가
            String newToken = "xyz789";
            return client.get().uri("https://example.org/another")
                    .retrieve()
                    .bodyToMono(String.class)
                    .contextWrite(context -> context.put("userToken", newToken));
        });

        // 결과를 구독하여 비동기적으로 처리
        response2.subscribe(responseBody -> {
            System.out.println("Final Response Body: " + responseBody);
            // 실제로는 이 부분에서 결과를 활용하거나 처리할 수 있음
            // 결과: "Final Response Body: [두 번째 요청의 응답 본문]"
        });
    }
}

```
