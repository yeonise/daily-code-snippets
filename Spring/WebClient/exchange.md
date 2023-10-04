## Exchange

The `exchangeToMono()` and `exchangeToFlux()` methods (or `awaitExchange { }` and `exchangeToFlow { }` in Kotlin) are useful for more advanced cases that require more control, such as to decode the response differently depending on the response status:

> `exchangeToMono()` 및 `exchangeToFlux()` 메서드(또는 Kotlin의 `awaitExchange { }` 및 `exchangeToFlow { }`)는 응답 상태에 따라 응답을 다르게 디코딩하는 등 더 많은 제어가 필요한 로직 구현에 유용합니다:

``` Java
Mono<Person> entityMono = client.get()
		.uri("/persons/1")
		.accept(MediaType.APPLICATION_JSON)
		.exchangeToMono(response -> {
			if (response.statusCode().equals(HttpStatus.OK)) {
				return response.bodyToMono(Person.class);
			}
			else {
				// Turn to error
				return response.createError();
			}
		});
```

When using the above, after the returned `Mono` or `Flux` completes, the response body is checked and if not consumed it is released to prevent memory and connection leaks. Therefore the response cannot be decoded further downstream. It is up to the provided function to declare how to decode the response if needed.

> 위의 방법을 사용하면, 반환된 `Mono` 또는 `Flux`가 완료된 후 응답 본문을 확인하고 사용되지 않은 경우 해제하여 메모리 및 연결 누수를 방지합니다.
> 따라서 응답은 더 이상 downstream에서 디코딩할 수 없습니다.
> 응답을 디코딩하는 방법을 선언하는 것은 제공된 함수에 달려 있습니다.
