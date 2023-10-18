## Synchronous Use

`WebClient` can be used in synchronous style by blocking at the end for the result:

> `WebClient`는 결과에 대해 마지막에 블로킹하여 동기식으로 사용할 수 있습니다.

``` java
Person person = client.get().uri("/person/{id}", i).retrieve()
	.bodyToMono(Person.class)
	.block();

List<Person> persons = client.get().uri("/persons").retrieve()
	.bodyToFlux(Person.class)
	.collectList()
	.block();
```

However if multiple calls need to be made, it’s more efficient to avoid blocking on each response individually, and instead wait for the combined result:

> 그러나 여러 번의 요청과 응답이 필요한 경우 각 응답을 개별적으로 차단하지 않고 합산된 결과를 기다리는 것이 더 효율적입니다:

``` java
Mono<Person> personMono = client.get().uri("/person/{id}", personId)
		.retrieve().bodyToMono(Person.class);

Mono<List<Hobby>> hobbiesMono = client.get().uri("/person/{id}/hobbies", personId)
		.retrieve().bodyToFlux(Hobby.class).collectList();

Map<String, Object> data = Mono.zip(personMono, hobbiesMono, (person, hobbies) -> {
			Map<String, String> map = new LinkedHashMap<>();
			map.put("person", person);
			map.put("hobbies", hobbies);
			return map;
		})
		.block();
```

The above is merely one example. There are lots of other patterns and operators for putting together a reactive pipeline that makes many remote calls, potentially some nested, interdependent, without ever blocking until the end.

> 위의 코드는 하나의 예시일 뿐입니다. 여러 원격 호출을 마지막 전까지 차단하지 않고 수행하는 리액티브 파이프라인을 구성하는 데는 다른 많은 패턴과 연산자가 있습니다.

```
With `Flux` or `Mono`, you should never have to block in a Spring MVC or Spring WebFlux controller. Simply return the resulting reactive type from the controller method. The same principle apply to Kotlin Coroutines and Spring WebFlux, just use suspending function or return `Flow` in your controller method .
```

> Flux 또는 Mono를 사용할 때에는  Spring MVC나 Spring WebFlux 컨트롤러에서 무조건 블로킹 작업을 하지 않아도 됩니다. 단순히 컨트롤러 메서드에서 반응형 타입을 반환하면 됩니다. Kotlin 코루틴과 Spring WebFlux에서도 동일한 원칙이 적용되며, 컨트롤러 메서드에서 중단 가능한 함수를 사용하거나 Flow를 반환하면 됩니다.
