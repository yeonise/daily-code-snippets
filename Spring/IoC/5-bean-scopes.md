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

Only one shared instance of a singleton bean is managed, and all requests for beans with an ID or IDs that match that
bean definition result in that one specific bean instance being returned by the Spring container.

> 싱글톤 bean의 공유 인스턴스는 하나만 관리되며, 해당 bean 정의와 일치하는 ID를 가진 bean에 대한 모든 요청은 해당 특정 bean 인스턴스 하나만 스프링 컨테이너에서 반환됩니다.

<br>

To put it another way, when you define a bean definition and it is scoped as a singleton, the Spring IoC container
creates exactly one instance of the object defined by that bean definition. This single instance is stored in a cache of
such singleton beans, and all subsequent requests and references for that named bean return the cached object. The
following image shows how the singleton scope works:

> 다시 말해, bean 정의를 ㅓㅈㅇ의하고 해당 정의가 싱글톤으로 범위가 지정되면, 스프링 IoC 컨테이너는 해당 bean 정의에 의해 정의된 객체의 인스턴스를 정확히 하나만 생성합니다. 이 단일 인스턴스는 이러한
> 싱글톤 bean의 캐시에 저장되며, 해당 명명된 bean에 대한 모든 후속 요청 및 참조는 캐시된 객체를 반환합니다. 다음 이미지는 싱글톤 범위의 작동 방식을 보여줍니다:

<br>

![](https://docs.spring.io/spring-framework/reference/_images/singleton.png)

<br>

Spring’s concept of a singleton bean differs from the singleton pattern as defined in the Gang of Four (GoF) patterns
book. The GoF singleton hard-codes the scope of an object such that one and only one instance of a particular class is
created per ClassLoader. The scope of the Spring singleton is best described as being per-container and per-bean. This
means that, if you define one bean for a particular class in a single Spring container, the Spring container creates one
and only one instance of the class defined by that bean definition. The singleton scope is the default scope in Spring.
To define a bean as a singleton in XML, you can define a bean as shown in the following example:

> 스프링의 싱글톤 bean 개념은 GoF(Gang of Four) 패턴 책에 정의된 싱글톤 패턴과 다릅니다. GoF 싱글톤은 객체의 범위를 하드 코딩하여 특정 클래스의 인스턴스가 ClassLoader당 하나만
> 생성되도록 합니다. 스프링 싱글톤의 범위는 컨테이너별 및 bean별로 설명하는 것이 가장 좋습니다. 즉, 단일 스프링 컨테이너에서 특정 클래스에 대해 하나의 bean을 정의하면, 스프링 컨테이너는 해당 bean
> 정의에 의해 정의된 클래스의 인스턴스를 하나만 생성합니다. 싱글톤 범위는 스프링의 기본 범위입니다. XML에서 bean을 싱글톤으로 정의하려면, 아래의 예제와 같이 빈을 정의할 수 있습니다:

```xml
<bean id="accountService" class="com.something.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<!-- 아래는 중복되지만, 동등합니다 (싱글톤 범위가 기본값입니다) -->
<bean id="accountService" class="com.something.DefaultAccountService" scope="singleton"/>
```

<br>

## The Prototype Scope

The non-singleton prototype scope of bean deployment results in the creation of a new bean instance every time a request
for that specific bean is made. That is, the bean is injected into another bean or you request it through a getBean()
method call on the container. As a rule, you should use the prototype scope for all stateful beans and the singleton
scope for stateless beans.

> 싱글톤이 아닌 프로토타입 범위의 bean 배포는 특정 bean에 대한 요청이 있을 때마다 새로운 bean 인스턴스를 생성합니다. 즉, bean이 다른 bean에 주입되거나 컨테이너에서 `getBean()` 메서드
> 호출을 통해 요청됩니다. 일반적으로 모든 stateful bean들에게는 프로토타입 범위를 사용하고, stateless bean에는 싱글톤 범위를 사용해야 합니다.

<br>

The following diagram illustrates the Spring prototype scope:

> 아래의 다이어그램은 스프링 프로토타입 범위를 보여줍니다:

![](https://docs.spring.io/spring-framework/reference/_images/prototype.png)

<br>

(A data access object (DAO) is not typically configured as a prototype, because a typical DAO does not hold any
conversational state. It was easier for us to reuse the core of the singleton diagram.)

> (데이터 액세스 객체(DAO)는 일반적으로 포로토타입으로 구성되지 않는데, 이는 일반적인 DAO가 대화 상태를 보유하지 않기 때문입니다. 싱글톤 다이어그램의 핵심을 재사용하는 것이 더 쉬웠습니다.)

<br>

The following example defines a bean as a prototype in XML:

> 아래의 예제는 XML에서 bean을 프로토타입으로 정의합니다:

```xml
<bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
```

<br>

In contrast to the other scopes, Spring does not manage the complete lifecycle of a prototype bean. The container
instantiates, configures, and otherwise assembles a prototype object and hands it to the client, with no further record
of that prototype instance. Thus, although initialization lifecycle callback methods are called on all objects
regardless of scope, in the case of prototypes, configured destruction lifecycle callbacks are not called. The client
code must clean up prototype-scoped objects and release expensive resources that the prototype beans hold. To get the
Spring container to release resources held by prototype-scoped beans, try using a custom bean post-processor, which
holds a reference to beans that need to be cleaned up.

> 다른 범위와 달리, 스프링은 프로토타입 bean의 전체 라이프사이클을 관리하지 않습니다. 컨테이너는 프로토타입 객체를 인스턴스화, 구성 및 기타 방식으로 모은 후 클라이언트에 전달하며, 해당 프로토타입 인스턴스에
> 대한 추가 기록은 남기지 않습니다. 따라서, 초기화 라이프사이클 콜백 메서드는 범위에 관계없이 모든 객체에서 호출되지만, 프로토타입의 경우 구성된 소멸 라이프사이클 콜백은 호출되지 않습니다. 클라이언트 코드는
> 프로토타입 범위의 객체를 정리하고, 프로토타입 bean이 보유하고 있는 값비싼 리소스를 해제해야 합니다. 스프링 컨테이너가 프로토타입 범위의 빈이 보유한 리소스를 해제하도록 하려면 정리해야 하는 빈에 대한 참조가
> 있는 커스텀 bean post-processor를 사용해 보세요.

<br>

In some respects, the Spring container’s role in regard to a prototype-scoped bean is a replacement for the Java new
operator. All lifecycle management past that point must be handled by the client. (For details on the lifecycle of a
bean in the Spring container, see Lifecycle Callbacks.)

> 어떤 측면에서는, 프로토타입 범위의 bean에 대한 스프링 컨테이너의 역할이 자바의 새로운 연산자를 대체합니다. 그 이후의 모든 라이프사이클 관리는 클라이언트에서 처리해야 합니다. (스프링 컨테이너에서 bean의
> 라이프사이클에 대한 자세한 내용은 라이프사이클 콜백을 참조하세요.)

<br>

## Singleton Beans with Prototype-bean Dependencies

다음 이 시간에..