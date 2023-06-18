## DispatcherServlet

Spring MVC, as many other web frameworks, is designed around the front controller pattern where a central `Servlet`, the `DispatcherServlet`, provides a shared algorithm for request processing, while actual work is performed by configurable delegate components. This model is flexible and supports diverse workflows.

> 다른 웹 프레임워크와 마찬가지로 스프링 MVC는 중앙 `Sevlet`인 `DiapathcerServlet`을 사용하는 프론트 컨트롤러 패턴을 바탕으로 디자인 되었습니다. `DiapathcerServlet`은 요청 처리를 위한 공통된 알고리즘을 제공하고 실질적인 작업은 각각의 컴포넌트들에게 위임합니다. 이러한 모델은 유연하고 다양한 작업 흐름을 가능하게 합니다.

The `DispatcherServlet`, as any `Servlet`, needs to be declared and mapped according to the Servlet specification by using Java configuration or in `web.xml`. In turn, the `DispatcherServlet` uses Spring configuration to discover the delegate components it needs for request mapping, view resolution, exception handling, and more.

> 다른 `Servlet`처럼 `DispatcherServlet`은 Java의 설정 파일 또는 `web.xml`에 Servlet 사양에 따라 매핑되고 선언되어야 합니다. 차례로, `DispatcherServlet`은 스프링 구성을 사용하여 요청 매핑, 뷰 반환, 예외 처리 등에 필요한 컴포넌트들을 검색합니다.

The following example of the Java configuration registers and initializes the `DispatcherServlet`, which is auto-detected by the Servlet container (see Servlet Config):

> 다음은 서블릿 컨테이너에 의해 자동으로 자바 설정을 등록하고 `DispatcherServlet`을 초기화하는 것에 대한 예시입니다.

``` java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

	@Override
	public void onStartup(ServletContext servletContext) {

		// Load Spring web application configuration
        // Spring 웹 애플리케이션 구성 로드
		AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
		context.register(AppConfig.class);

		// Create and register the DispatcherServlet
        // DispatcherServlet 생성 및 등록
		DispatcherServlet servlet = new DispatcherServlet(context);
		ServletRegistration.Dynamic registration = servletContext.addServlet("app", servlet);
		registration.setLoadOnStartup(1);
		registration.addMapping("/app/*");
	}
}
```

In addition to using the ServletContext API directly, you can also extend AbstractAnnotationConfigDispatcherServletInitializer and override specific methods.

> ServletContext API를 직접 사용하는 것 외에도 `AbstractAnnotationConfigDispatcherServletInitializer`를 상속하여 특정 메서드를 재정의할 수도 있습니다.

For programmatic use cases, a GenericWebApplicationContext can be used as an alternative to AnnotationConfigWebApplicationContext. See the `GenericWebApplicationContext` javadoc for details.

> 프로그래밍 사용 사례의 경우 `GenericWebApplicationContext`를 `AnnotationConfigWebApplicationContext`의 대안으로 사용할 수 있습니다. 자세한 내용은 `GenericWebApplicationContext` javadoc을 참조하세요.

The following example of web.xml configuration registers and initializes the DispatcherServlet:

> 다음 예시의 web.xml 설정은 DispatcherServlet을 등록하고 초기화합니다.

``` xml
<web-app>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/app-context.xml</param-value>
	</context-param>

	<servlet>
		<servlet-name>app</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value></param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>app</servlet-name>
		<url-pattern>/app/*</url-pattern>
	</servlet-mapping>

</web-app>
```

Spring Boot follows a different initialization sequence. Rather than hooking into the lifecycle of the Servlet container, Spring Boot uses Spring configuration to bootstrap itself and the embedded Servlet container. Filter and Servlet declarations are detected in Spring configuration and registered with the Servlet container. 

> 스프링 부트는 서블릿 컨테이너의 라이프사이클에 훅을 거는 대신에 스프링 구성을 사용하여 자체적으로 부트스트랩하고 내장된 서블릿 컨테이너를 초기화합니다. 이를 위해 스프링 부트는 스프링 구성에서 Filter와 Servlet 선언을 감지하고, 이를 서블릿 컨테이너에 등록합니다.

> 일반적으로, 웹 애플리케이션을 개발할 때에는 서블릿 컨테이너의 라이프사이클 이벤트를 사용하여 애플리케이션을 초기화하고, 필터 및 서블릿을 등록합니다. 그러나 Spring Boot는 이와는 다른 방식으로 동작합니다. Spring Boot는 내장된 서블릿 컨테이너를 사용하여 애플리케이션을 실행하며, 서블릿 컨테이너의 라이프사이클 이벤트에 직접적으로 의존하지 않습니다.  
Spring Boot는 스프링 구성 파일(예: XML 또는 JavaConfig)을 사용하여 애플리케이션의 구성을 정의합니다. 이 구성 파일에서 Filter와 Servlet을 선언하고, Spring Boot는 이를 검색하여 내장된 서블릿 컨테이너에 등록합니다. 따라서 Spring Boot 애플리케이션은 서블릿 컨테이너를 독립적으로 실행할 수 있으며, 서블릿 컨테이너의 특정 라이프사이클 이벤트에 의존하지 않아도 됩니다.
이러한 방식은 Spring Boot의 간편성과 유연성을 제공합니다. 개발자는 Spring 구성 파일을 통해 필터와 서블릿을 쉽게 등록하고, Spring Boot는 이를 자동으로 처리하여 서블릿 컨테이너에 적용합니다. 이를 통해 개발자는 애플리케이션 초기화 및 서블릿 컨테이너 설정에 대한 복잡한 작업을 줄일 수 있습니다.
