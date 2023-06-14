# Bean Scopes

When you create a bean definition, you create a recipe for creating actual instances of the class defined by that bean
definition. The idea that a bean definition is a recipe is important, because it means that, as with a class, you can
create many object instances from a single recipe.

> bean 정의를 생성하면, 해당 bean 정의에 정의된 클래스의 실제 인스턴스를 생성하기 위한 레시피가 생성됩니다. bean 정의가 레시피라는 개념이 중요한 이유는 클래스와 마찬가지로 싱글 레시피에서 많은 객체
> 인스턴스를 생성할 수 있기 때문입니다.

<br>

You can control not only the various dependencies and configuration values that are to be plugged into an object that is
created from a particular bean definition but also control the scope of the objects created from a particular bean
definition. This approach is powerful and flexible, because you can choose the scope of the objects you create through
configuration instead of having to bake in the scope of an object at the Java class level. Beans can be defined to be
deployed in one of a number of scopes. The Spring Framework supports six scopes, four of which are available only if you
use a web-aware ApplicationContext. You can also create a custom scope.

> 특정 bean 정의에서 생성되는 객체에 연결할 다양한 의존성 및 구성 값을 제어할 수 있을 뿐만 아니라, 특정 bean 정의에서 생성되는 객체의 범위도 제어할 수 있습니다. 이 접근 방식은 자바 클래스 수준에서
> 객체의 범위를 `bake`할 필요 없이, 구성을 통해 생성하는 객체의 범위를 선택할 수 있으므로 강력하고 유연합니다. bean은 여러 범위 중 하나에 배포되도록 정의할 수 있습니다. 스프링 프레임워크는 6개의
> 스코프를 지원하며, 이 중 4개는 웹 인식 `ApplicationContext`를 사용하는 경우에만 사용할 수 있습니다. 또한 `custom scope`를 생성할 수도 있습니다.

<br>

The following table describes the supported scopes:

> 아래의 표에서는 지원되는 `Scope`에 대해 설명합니다.

| Scope         | Description                                                                                                                                                                                                                                                |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `singleton`   | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container.                                                                                                                                                       |
| `prototype`   | Scopes a single bean definition to any number of object instances.                                                                                                                                                                                         |
| `request`     | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring ApplicationContext. |
| `session`     | Scopes a single bean definition to the lifecycle of an HTTP Session. Only valid in the context of a web-aware Spring ApplicationContext.                                                                                                                   |
| `application` | Scopes a single bean definition to the lifecycle of a ServletContext. Only valid in the context of a web-aware Spring ApplicationContext.                                                                                                                  |
| `websocket`   | Scopes a single bean definition to the lifecycle of a WebSocket. Only valid in the context of a web-aware Spring ApplicationContext.                                                                                                                       |

| Scope         | Description                                                                                                                                   |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| `singleton`   | (기본값) 단일 bean 정의의 범위를 각 스프링 IOC 컨테이너에 대한 단일 객체 인스턴스로 제한합니다.                                                                                   |
| `prototype`   | 단일 bean 정의를 원하는 수의 객체 인스턴스로 범위를 지정합니다.                                                                                                        |
| `request`     | 단일 bean 정의를 단일 HTTP 요청의 라이프사이클로 범위를 지정합니다. 즉, 각 HTTP 요청에는 단일 bean 정의의 백그라운드에서 생성된 자체 bean 인스턴스가 있습니다. 웹 인식 스프링 `ApplicationContext`에서만 유효합니다. |
| `session`     | 단일 bean 정의를 HTTP 세션의 라이프사이클로 범위를 지정합니다. 웹 인식 스프링 `ApplicationContext`에서만 유효합니다.                                                               |
| `application` | 단일 bean 정의를 `ServletContext`의 라이프사이클로 범위를 지정합니다. 웹 인식 스프링 `ApplicationContext`에서만 유효합니다.                                                      |
| `websocket`   | 단일 bean 정의를 `WebSocket`의 라이프사이클로 범위를 지정합니다. 웹 인식 스프링 `ApplicationContext`의 컨텍스트에서만 유효합니다.                                                     |

<br>

A thread scope is available but is not registered by default. For more information, see the documentation for
SimpleThreadScope. For instructions on how to register this or any other custom scope, see Using a Custom Scope.
{: .notice--primary}

> 스레드 범위를 사용할 수 있지만 기본적으로는 등록되어 있지 않습니다. 자세한 내용은 `SimpleThreadScope` 설명서를 참조하세요. 이 범위 또는 다른 사용자 지정 범위를 등록하는 방법에 대한
> 지침은 `Using a Custom Scope`를 참조하세요.

<br>

## The Singleton Scope

다음 이 시간에..