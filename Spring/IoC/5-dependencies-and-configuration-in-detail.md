# Dependencies and Configuration in Detail

As mentioned in the previous section, you can define bean properties and constructor arguments as references to other
managed beans (collaborators) or as values defined inline. Spring’s XML-based configuration metadata supports
sub-element types within its <property/> and <constructor-arg/> elements for this purpose.

> 이전 섹션에서 언급했듯이, 빈 프로퍼티와 생성자 인자를 다른 관리되는 빈(콜라보레이터)에 대한 참조로 정의하거나 인라인으로 정의된 값을 정의할 수 있습니다. 스프링의 XML 기반 구성 메타데이터는 이러한 목적을
> 위해 `<property/>` 및 `<constructor-arg/>` 요소 내에서 하위 요소 유형을 지원합니다.

<br>

## Straight Values (Primitives, Strings, and so on)

The value attribute of the <property/> element specifies a property or constructor argument as a human-readable string
representation. Spring’s conversion service is used to convert these values from a String to the actual type of the
property or argument. The following example shows various values being set:

> `<property/>` 요소의 값 속성은 프로퍼티 또는 생성자 인자를 사람이 읽을 수 있는 문자열 표현으로 지정합니다. 스프링의 변환 서비스는 이러한 값을 `String`에서 프로퍼티 또는 인자의 실제 유형으로
> 반환되는 데 사용됩니다. 아래의 예제는 다양한 값이 설정되는 것을 보여줍니다:

```xml
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<!-- results in a setDriverClassName(String) call -->
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
	<property name="username" value="root"/>
	<property name="password" value="misterkaoli"/>
</bean>
```

<br>

The following example uses the p-namespace for even more succinct XML configuration:

> 아래의 예제에서는 더욱 간결한 XML 구성을 위해 `p-namesapce`를 사용합니다:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close"
		p:driverClassName="com.mysql.jdbc.Driver"
		p:url="jdbc:mysql://localhost:3306/mydb"
		p:username="root"
		p:password="misterkaoli"/>

</beans>
```

The preceding XML is more succinct. However, typos are discovered at runtime rather than design time, unless you use an
IDE (such as IntelliJ IDEA or the Spring Tools for Eclipse) that supports automatic property completion when you create
bean definitions. Such IDE assistance is highly recommended.

> 앞의 XML이 더 간결합니다. 그러나 빈 정의를 생성할 때 자동 속성 완성을 지원하는 IDE를 사용하지 않는 한 오타가 설계 시점이 아닌, 런타임에 발견됩니다. 이러한 IDE 지원을 적극 권장합니다. 알겠습니다!

<br>

You can also configure a java.util.Properties instance, as follows:

> 다음과 같이 `java.util.Preperties` 인스턴스를 구성할 수도 있습니다:

```xml
<bean id="mappings"
	class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">

	<!-- typed as a java.util.Properties -->
	<property name="properties">
		<value>
			jdbc.driver.className=com.mysql.jdbc.Driver
			jdbc.url=jdbc:mysql://localhost:3306/mydb
		</value>
	</property>
</bean>
```

The Spring container converts the text inside the <value/> element into a java.util.Properties instance by using the
JavaBeans PropertyEditor mechanism. This is a nice shortcut, and is one of a few places where the Spring team do favor
the use of the nested <value/> element over the value attribute style.

> 스프링 컨테이너는 JavaBeans `PrepertiyEditor` 메커니즘을 사용하여 `<values>` 요소 내부의 텍스트를 `java.util.Properties` 인스턴스로 변환합니다. 이것은 좋은
> 지름길이며, 스프링 팀에서 값 속성 스타일보다 중첩된 `<value/>` 요소의 사용을 선호하는 몇 안 되는 곳 중 하나입니다.

<br>

추가 예정..