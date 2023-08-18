## Filters

The `spring-web` module provides some useful filters:

> `spring-web` 모듈은 몇몇의 유용한 필터들을 제공합니다:

- Form Data

- Forwarded Headers

- Shallow ETag

- CORS

### Form Data

Browsers can submit form data only through HTTP GET or HTTP POST but non-browser clients can also use HTTP PUT, PATCH, and DELETE. The Servlet API requires `ServletRequest.getParameter*()` methods to support form field access only for HTTP POST.

> 브라우저는 폼 데이터를 HTTP GET 또는 POST 방식을 통해서만 제출할 수 있습니다. 브라우저가 아닌 클라이언트들은 HTTP PUT, PATCH, DELETE 또한 사용할 수 있습니다. 서블릿 API는 HTTP POST에 대해서는 폼 필드 접근을 지원하기 위해 `ServletRequest.getParameter*()` 메서드가 필요합니다.

The `spring-web` module provides `FormContentFilter` to intercept HTTP PUT, PATCH, and DELETE requests with a content type of `application/x-www-form-urlencoded`, read the form data from the body of the request, and wrap the `ServletRequest` to make the form data available through the `ServletRequest.getParameter*()` family of methods.

> `spring-web` 모듈은 `application/x-www-form-urlencoded` 타입의 HTTP PUT, PATCH, DELETE 요청을 인터셉트하고, 요청 본문에서 폼 데이터를 읽고 `ServletRequest`를 래핑하여 `ServletRequest.getParameter*()` 메서드를 통해 사용 가능한 데이터로 만드는 `FormContentFilter`를 제공합니다.

### Forwarded Headers

As a request goes through proxies (such as load balancers) the host, port, and scheme may change, and that makes it a challenge to create links that point to the correct host, port, and scheme from a client perspective.

> 요청이 프록시(로드 밸런서와 같은 것들)를 통과함에 따라 호스트, 포트 및 구성표가 변경될 수 있으므로 클라이언트 관점에서 올바른 호스트, 포트 및 구성표를 가리키는 링크를 만드는 것은 어렵습니다.

RFC 7239 defines the `Forwarded` HTTP header that proxies can use to provide information about the original request. There are other non-standard headers, too, including `X-Forwarded-Host`, `X-Forwarded-Port`, `X-Forwarded-Proto`, `X-Forwarded-Ssl`, and `X-Forwarded-Prefix`.

> RFC 7239는 `Forwarded`라는 프록시가 원래 요청에 대한 정보를 제공하는 데 사용할 수 있는 HTTP header를 규정했습니다. `X-Forwarded-Host`, `X-Forwarded-Port`, `X-Forwarded-Proto`, `X-Forwarded-Ssl`, and `X-Forwarded-Prefix`를 비롯한 다른 비표준 헤더들도 있습니다.

`ForwardedHeaderFilter` is a Servlet filter that modifies the request in order to a) change the host, port, and scheme based on `Forwarded` headers, and b) to remove those headers to eliminate further impact. The filter relies on wrapping the request, and therefore it must be ordered ahead of other filters, such as `RequestContextFilter`, that should work with the modified and not the original request.

> `ForwardedHeaderFilter`는 요청을 수정하여 a) 전달된 헤더에 기반하여 호스트, 포트 및 프로토콜을 변경하고, b) 해당 헤더를 제거하여 추가적인 영향을 없애는 Servlet 필터입니다. 이 필터는 요청을 래핑하기 때문에 수정된 요청과 원래의 요청을 사용해야 하는 `RequestContextFilter`와 같은 다른 필터보다 먼저 적용되어야 합니다.

There are security considerations for forwarded headers since an application cannot know if the headers were added by a proxy, as intended, or by a malicious client. This is why a proxy at the boundary of trust should be configured to remove untrusted Forwarded headers that come from the outside. You can also configure the ForwardedHeaderFilter with removeOnly=true, in which case it removes but does not use the headers.

In order to support asynchronous requests and error dispatches this filter should be mapped with DispatcherType.ASYNC and also DispatcherType.ERROR. If using Spring Framework’s AbstractAnnotationConfigDispatcherServletInitializer (see Servlet Config) all filters are automatically registered for all dispatch types. However if registering the filter via web.xml or in Spring Boot via a FilterRegistrationBean be sure to include DispatcherType.ASYNC and DispatcherType.ERROR in addition to DispatcherType.REQUEST.

### Shallow ETag

The ShallowEtagHeaderFilter filter creates a “shallow” ETag by caching the content written to the response and computing an MD5 hash from it. The next time a client sends, it does the same, but it also compares the computed value against the If-None-Match request header and, if the two are equal, returns a 304 (NOT_MODIFIED).

This strategy saves network bandwidth but not CPU, as the full response must be computed for each request. Other strategies at the controller level, described earlier, can avoid the computation. See HTTP Caching.

This filter has a writeWeakETag parameter that configures the filter to write weak ETags similar to the following: W/"02a2d595e6ed9a0b24f027f2b63b134d6" (as defined in RFC 7232 Section 2.3).

In order to support asynchronous requests this filter must be mapped with DispatcherType.ASYNC so that the filter can delay and successfully generate an ETag to the end of the last async dispatch. If using Spring Framework’s AbstractAnnotationConfigDispatcherServletInitializer (see Servlet Config) all filters are automatically registered for all dispatch types. However if registering the filter via web.xml or in Spring Boot via a FilterRegistrationBean be sure to include DispatcherType.ASYNC.

### CORS

Spring MVC provides fine-grained support for CORS configuration through annotations on controllers. However, when used with Spring Security, we advise relying on the built-in CorsFilter that must be ordered ahead of Spring Security’s chain of filters.
