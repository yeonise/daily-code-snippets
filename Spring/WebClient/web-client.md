```
스프링 부트 핵심 가이드, 장정우 저 (p.365~)

최신 버전에서는 RestTemplate이 지원 중단되어 WebClient를 사용할 것을 권고하고 있습니다. Spring WebFlux는 HTTP 요청을 수행하는 클라이언트로 WebClient를 제공합니다. WebClient는 Reactor 기반으로 동작하는 API입니다. Reactor 기반이므로 스레드와 동시성 문제를 벗어나 비동기 형식으로 사용할 수 있습니다.

- Non-Blocking I/O를 지원합니다.
- Reactive Streams의 Back Pressure를 지원합니다.
- 적은 하드웨어 리소스로 동시성을 지원합니다.
- 함수형 API를 지원합니다.
- 동기, 비동기 상호작용을 지원합니다.
- 스트리밍을 지원합니다.
```

Spring WebFlux includes a client to perform HTTP requests with. `WebClient` has a functional, fluent API based on Reactor, see Reactive Libraries, which enables declarative composition of asynchronous logic without the need to deal with threads or concurrency. It is fully non-blocking, it supports streaming, and relies on the same codecs that are also used to encode and decode request and response content on the server side.

> Spring WebFlux에는 HTTP 요청을 수행할 수 있는 클라이언트가 포함되어 있습니다.
> `WebClient`는 스레드나 동시성을 처리할 필요 없이 비동기 로직을 구성할 수 있는 Reactor 기반의 functional한 API가 있습니다. (리액티브 라이브러리 참조)
> 완전히 non-blocking으로 동작하며 스트리밍을 지원합니다. 또한 서버 측에서 요청 및 응답 콘텐츠를 인코딩하고 디코딩하는 데 사용되는 것과 동일한 codec을 사용합니다.

`WebClient` needs an HTTP client library to perform requests with. There is built-in support for the following:

> `WebClient`는 요청을 수행하기 위해 HTTP 클라이언트 라이브러리가 필요합니다. 다음 항목들을 기본적으로 지원합니다:

- Reactor Netty

- JDK HttpClient

- Jetty Reactive HttpClient

- Apache HttpComponents

- Others can be plugged via `ClientHttpConnector`.
