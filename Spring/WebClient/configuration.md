## Configuration

The simplest way to create a `WebClient` is through one of the static factory methods:

> `WebClient`를 생성하는 가장 간단한 방법은 아래의 정적 팩토리 메서드 중 하나를 사용하는 것입니다:

- `WebClient.create()`
- `WebClient.create(String baseUrl)`

You can also use `WebClient.builder()` with further options:

> `WebClient.builder()`를 사용하여 옵션을 추가할 수 있습니다:

- `uriBuilderFactory`: Customized `UriBuilderFactory` to use as a base URL.
- `defaultUriVariables`: default values to use when expanding URI templates.
- `defaultHeader`: Headers for every request.
- `defaultCookie`: Cookies for every request.
- `defaultRequest`: `Consumer` to customize every request.
- `filter`: Client filter for every request.
- `exchangeStrategies`: HTTP message reader/writer customizations.
- `clientConnector`: HTTP client library settings.
- `observationRegistry`: the registry to use for enabling `Observability` support.
- `observationConvention`: an optional, custom convention to extract metadata for recorded observations.

For example:

``` Java
WebClient client = WebClient.builder()
		.codecs(configurer -> ... )
		.build();
```

Once built, a `WebClient` is immutable. However, you can clone it and build a modified copy as follows:

> `WebClient`는 한번 빌드하면 변경할 수 없습니다. 하지만 다음과 같이 복제하여 수정된 사본을 빌드할 수 있습니다:

``` Java
// client1 has filterA, filterB
WebClient client1 = WebClient.builder()
		.filter(filterA).filter(filterB).build();

// client2 has filterA, filterB, filterC, filterD
WebClient client2 = client1.mutate()
		.filter(filterC).filter(filterD).build();
```

<br/>

### MaxInMemorySize

Codecs have limits for buffering data in memory to avoid application memory issues. By default those are set to 256KB. If that’s not enough you’ll get the following error:

> codec은 애플리케이션의 메모리 부족 문제를 피하기 위해 데이터 버퍼에 대한 제한을 갖고 있습니다. 기본값은 256KB로 설정되어 있습니다. 만약 충분하지 않다면 다음과 같은 에러가 발생합니다:

```
org.springframework.core.io.buffer.DataBufferLimitException: Exceeded limit on max bytes to buffer
```

To change the limit for default codecs, use the following:

> codec의 제한 기본값을 변경하려면 다음과 같이 설정합니다:

``` Java
WebClient webClient = WebClient.builder()
		.codecs(configurer -> configurer.defaultCodecs().maxInMemorySize(2 * 1024 * 1024))
		.build();
```

<br/>

### Reactor Netty

To customize Reactor Netty settings, provide a pre-configured `HttpClient`:

> Reactor Netty 사용자 정의하려면 미리 구성된 `HttpClient`가 필요합니다:

``` Java
HttpClient httpClient = HttpClient.create().secure(sslSpec -> ...);

WebClient webClient = WebClient.builder()
		.clientConnector(new ReactorClientHttpConnector(httpClient))
		.build();
```

#### Resources

By default, `HttpClient` participates in the global Reactor Netty resources held in `reactor.netty.http.HttpResources`, including event loop threads and a connection pool. This is the recommended mode, since fixed, shared resources are preferred for event loop concurrency. In this mode global resources remain active until the process exits.

> 기본적으로 `HttpClient`는 이벤트 루프 스레드 및 연결 풀을 포함하여 `reactor.netty.http.HttpResources`에 보관된 글로벌 Reactor Netty 리소스에 참여합니다. 이벤트 루프 동시성을 위해 고정된 공유 리소스가 선호되므로 해당 모드를 권장합니다. 이 모드에서는 전역 리소스가 프로세스가 종료될 때까지 활성 상태로 유지됩니다.

If the server is timed with the process, there is typically no need for an explicit shutdown. However, if the server can start or stop in-process (for example, a Spring MVC application deployed as a WAR), you can declare a Spring-managed bean of type `ReactorResourceFactory` with `globalResources=true` (the default) to ensure that the Reactor Netty global resources are shut down when the Spring `ApplicationContext` is closed, as the following example shows:

> 서버가 프로세스와 함께 시간이 지정된 경우, 명시적으로 종료할 필요는 없습니다. 그러나, 서버가 프로세스 중에 시작하거나 종료할 수 있는 경우(예를 들어, WAR로 배포된 Spring MVC 애플리케이션과 같이), `globalResources=true(기본값)`로 설정한 `ReactorResourceFactory` 유형의 스프링이 관리하는 빈을 선언하여 Spring `ApplicationContext`가 닫힐 때 Reactor Netty 글로번 자원 또한 종료되도록 할 수 있습니다.

``` Java
@Bean
public ReactorResourceFactory reactorResourceFactory() {
	return new ReactorResourceFactory();
}
```

You can also choose not to participate in the global Reactor Netty resources. However, in this mode, the burden is on you to ensure that all Reactor Netty client and server instances use shared resources, as the following example shows:

> 또한 글로벌 Reactor Netty 자원에 참여하지 않도록 선택할 수 있습니다. 그러나 해당 모드에서는 모든 Reactor Netty 클라이언트 및 서버 인스턴스가 공유 리소스를 사용하도록 설정해야 하는 부담이 있습니다:

``` Java
@Bean
public ReactorResourceFactory resourceFactory() {
	ReactorResourceFactory factory = new ReactorResourceFactory();
    // 글로벌 리소스와 독립적으로 리소스를 생성
	factory.setUseGlobalResources(false);
	return factory;
}

@Bean
public WebClient webClient() {

	Function<HttpClient, HttpClient> mapper = client -> {
		// Further customizations...
	};

	ClientHttpConnector connector =
			new ReactorClientHttpConnector(resourceFactory(), mapper);

	return WebClient.builder().clientConnector(connector).build();
}
```

#### Timeouts

To configure a connection timeout:

> 연결에 대한 시간 초과를 정의하려면:

``` Java
import io.netty.channel.ChannelOption;

HttpClient httpClient = HttpClient.create()
		.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 10000);

WebClient webClient = WebClient.builder()
		.clientConnector(new ReactorClientHttpConnector(httpClient))
		.build();
```

To configure a read or write timeout:

> 읽기 또는 쓰기 작업에 대한 시간 초과를 정의하려면:

``` Java
import io.netty.handler.timeout.ReadTimeoutHandler;
import io.netty.handler.timeout.WriteTimeoutHandler;

HttpClient httpClient = HttpClient.create()
		.doOnConnected(conn -> conn
				.addHandlerLast(new ReadTimeoutHandler(10))
				.addHandlerLast(new WriteTimeoutHandler(10)));

// Create WebClient...
```

To configure a response timeout for all requests:

> 모든 요청에 대한 응답의 시간 초과를 정의하려면:

``` Java
HttpClient httpClient = HttpClient.create()
		.responseTimeout(Duration.ofSeconds(2));

// Create WebClient...
```

To configure a response timeout for a specific request:

> 특정 요청에 대한 응답의 시간 초과를 정의하려면:

``` Java
WebClient.create().get()
		.uri("https://example.org/path")
		.httpRequest(httpRequest -> {
			HttpClientRequest reactorRequest = httpRequest.getNativeRequest();
			reactorRequest.responseTimeout(Duration.ofSeconds(2));
		})
		.retrieve()
		.bodyToMono(String.class);
```

<br/>

### JDK HttpClient

The following example shows how to customize the JDK `HttpClient`:

> 다음은 JDK `HttpClient`를 사용자 정의하는 예시입니다:

``` Java
HttpClient httpClient = HttpClient.newBuilder()
    .followRedirects(Redirect.NORMAL)
    .connectTimeout(Duration.ofSeconds(20))
    .build();

ClientHttpConnector connector =
        new JdkClientHttpConnector(httpClient, new DefaultDataBufferFactory());

WebClient webClient = WebClient.builder().clientConnector(connector).build();
```

<br/>

### Jetty

The following example shows how to customize Jetty `HttpClient` settings:

> 다음은 Jetty `HttpClient`를 사용자 정의하는 예시입니다:

``` Java
HttpClient httpClient = new HttpClient();
httpClient.setCookieStore(...);

WebClient webClient = WebClient.builder()
		.clientConnector(new JettyClientHttpConnector(httpClient))
		.build();
```

By default, `HttpClient` creates its own resources (`Executor`, `ByteBufferPool`, `Scheduler`), which remain active until the process exits or `stop()` is called.

> 기본적으로 `HttpClient`는 프로세스가 끝나거나 `stop()`이 호출되기 전까지 활성 상태로 유지되는 자체 리소스들(`Executor`, `ByteBufferPool`, `Scheduler`)을 생성합니다. 

You can share resources between multiple instances of the Jetty client (and server) and ensure that the resources are shut down when the Spring `ApplicationContext` is closed by declaring a Spring-managed bean of type `JettyResourceFactory`, as the following example shows:

> 다음 예제에서 볼 수 있듯이, 여러 Jetty 클라이언트(및 서버) 인스턴스 간에 리소스를 공유할 수 있으며, `JettyResourceFactory` 유형의 스프링이 관리하는 빈을 선언하여 Spring `ApplicationContext`가 종료될 때 리소스가 종료되도록 할 수 있습니다:

``` Java
@Bean
public JettyResourceFactory resourceFactory() {
	return new JettyResourceFactory();
}

@Bean
public WebClient webClient() {

	HttpClient httpClient = new HttpClient();
	// Further customizations...

	ClientHttpConnector connector =
			new JettyClientHttpConnector(httpClient, resourceFactory());

	return WebClient.builder().clientConnector(connector).build();
}
```

<br/>

### HttpComponents

The following example shows how to customize Apache HttpComponents `HttpClient` settings:

> 다음은 Apache HttpComponents HttpClient 설정을 사용자 정의하는 방법에 대한 예시입니다:

``` Java
HttpAsyncClientBuilder clientBuilder = HttpAsyncClients.custom();
clientBuilder.setDefaultRequestConfig(...);
CloseableHttpAsyncClient client = clientBuilder.build();

ClientHttpConnector connector = new HttpComponentsClientHttpConnector(client);

WebClient webClient = WebClient.builder().clientConnector(connector).build();
```
