# Method Injection

In most application scenarios, most beans in the container are singletons. When a singleton bean needs to collaborate
with another singleton bean or a non-singleton bean needs to collaborate with another non-singleton bean, you typically
handle the dependency by defining one bean as a property of the other. A problem arises when the bean lifecycles are
different. Suppose singleton bean A needs to use non-singleton (prototype) bean B, perhaps on each method invocation on
A. The container creates the singleton bean A only once, and thus only gets one opportunity to set the properties. The
container cannot provide bean A with a new instance of bean B every time one is needed.

> 대부분의 애플리케이션 시나리오에서, 컨테이너의 대부분의 bean은 싱글톤입니다. 싱글톤 bean이 다른 싱글톤 bean과 협업해야 하거나 비싱글톤 bean이 다른 비싱글톤 bean과 엽헙해야 하는 경우,
> 일반적으로 한 bean을 다른 bean의 속성으로 정의하여 의존성을 처리합니다. 문제는 bean의 라이프사이클이 다를 때 발생합니다. 싱글톤 bean A가 A에서 메서드를 호출할 때마다 비싱글톤(프로토타입)
> bean
> B를 사용해야 한다고 가정해 보겠습니다. 컨테이너는 싱글톤 bean A를 한 번만 생성하므로, 속성을 설정할 기회는 한 번만 얻게 됩니다. 컨테이너는 필요할 때마다 bean A에 bean B의 새 인스턴스를
> 제공할
> 수 있습니다:

<br>

A solution is to forego some inversion of control. You can make bean A aware of the container by implementing the
ApplicationContextAware interface, and by making a getBean("B") call to the container ask for (a typically new) bean B
instance every time bean A needs it. The following example shows this approach:

> 한 가지 해결책은 `IoC`를 포기하는 것입니다. bean A가 컨테이너를 인식하게 하려면 `ApplicationContextAware` 인터페이스를 구현하고, bean A가 필요할 때마다 컨테이너에
> 대한 `getBean("B")` 호출이 (일반적으로 새로운) bean B 인스턴스를 요청하도록 하면 됩니다. 아래의 예제는 이 접근 방식을 보여줍니다:ㅣ

```java
package fiona.apple;

// Spring-API imports
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

/**
 * A class that uses a stateful Command-style class to perform
 * some processing.
 * stateful 커맨드-스타일 클래스를 사용하여 일부 처리를 수행하는 클래스
 */
public class CommandManager implements ApplicationContextAware {

	private ApplicationContext applicationContext;

	public Object process(Map commandState) {
		// grab a new instance of the appropriate Command
		// 해당 명령의 새 인스턴스를 가져옵니다
		Command command = createCommand();
		// set the state on the (hopefully brand new) Command instance
		// (새로운) 커맨드 인스턴스에 상태를 설정합니다.
		command.setState(commandState);
		return command.execute();
	}

	protected Command createCommand() {
		// notice the Spring API dependency!
		// 스프링 API 의존성에 주목!
		return this.applicationContext.getBean("command", Command.class);
	}

	public void setApplicationContext(
			ApplicationContext applicationContext) throws BeansException {
		this.applicationContext = applicationContext;
	}
}
```

The preceding is not desirable, because the business code is aware of and coupled to the Spring Framework. Method
Injection, a somewhat advanced feature of the Spring IoC container, lets you handle this use case cleanly.

> 비즈니스 코드가 스프링 프레임워크를 인식하고 스프링 프레임워크에 결합되어 있기 때문에 위의 방법은 바람직하지 않습니다. 스프링 IoC 컨테이너의 다소 고급 기능인 메서드 주입을 사용하면 위 코드를 깔끔하게 처리할
> 수 있습니다.

<br>

You can read more about the motivation for Method Injection in this blog entry.
{: .notice--primary}

> 메서드 주입에 대한 자세한 내용은 [이 블로그](https://spring.io/blog/2004/08/06/method-injection)에서 확인할 수 있습니다.

<br>

## Lookup Method Injection

다음 이 시간에..