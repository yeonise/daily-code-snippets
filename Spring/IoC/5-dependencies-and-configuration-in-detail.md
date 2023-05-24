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

### The idref element

The idref element is simply an error-proof way to pass the id (a string value - not a reference) of another bean in the
container to a <constructor-arg/> or <property/> element. The following example shows how to use it:

> `idref` 요소는 컨테이너에 있는 다른 빈의 `id`(참조가 아닌 문자열 값)를 `<constructor-arg/>` 또는 `<property/>` 요소로 전달하는 오류 방지 방식입니다. 아래의 예제는 그
> 사용 방법을 보여줍니다:

```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
	<property name="targetName">
		<idref bean="theTargetBean"/>
	</property>
</bean>
```

The preceding bean definition snippet is exactly equivalent (at runtime) to the following snippet:

> 위의 빈 정의 코드는 (실행 시) 아래의 코드와 정확히 동일합니다:

```xml
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
	<property name="targetName" value="theTargetBean"/>
</bean>
```

The first form is preferable to the second, because using the idref tag lets the container validate at deployment time
that the referenced, named bean actually exists. In the second variation, no validation is performed on the value that
is passed to the targetName property of the client bean. Typos are only discovered (with most likely fatal results) when
the client bean is actually instantiated. If the client bean is a prototype bean, this typo and the resulting exception
may only be discovered long after the container is deployed.

> 첫 번째 형태가 두 번째 형태보다 더 선호되는데, 이는 `idref` 태그를 사용하면 컨테이너가 배포 시점에 참조된 빈이 실제로 존재하는지 확인할 수 있기 때문입니다. 두 번째 변형에서는 `client`
> 빈의 `targetName` 속성에 전달되는 값에 대한 유효성 검사가 수행되지 않습니다. 오타는 `client` 빈이 실제로 인스턴스화될 때만 발견됩니다(대부분 치명적인 결과를 초래할 수 있음). 클라이언트 빈이
> 프로토타입 빈일 경우, 이 오타와 그에 따른 예외는 컨테이너가 배포된 후 한참 후에야 발견될 수 있습니다.

<br>

The local attribute on the idref element is no longer supported in the 4.0 beans XSD, since it does not provide value
over a regular bean reference any more. Change your existing idref local references to idref bean when upgrading to the
4.0 schema.
{: .notice--primary}

> `idref` 요소의 `local` 속성은 더 이상 일반 빈 참조에 대한 값을 제공하지 않으므로, 4.0 빈 XSD에서 더 이상 지원되지 않습니다. 4.0 스키마로 업그레이드할 때, 기존 `idref local`
> 참조를 `idref bean` 빈으로 변경하세요.

<br>

A common place (at least in versions earlier than Spring 2.0) where the <idref/> element brings value is in the
configuration of AOP interceptors in a ProxyFactoryBean bean definition. Using <idref/> elements when you specify the
interceptor names prevents you from misspelling an interceptor ID.

> `<idref/>` 요소가 가치를 제공하는 일반적인 위치(적어도 스프링 2.0 이전 버전)는 `ProxyFactoryBean` 빈 정의의 AOP 인터셉터 구성입니다. 인터셉터 이름을 지정할
> 때, `<idref/>` 요소를 사용하면 인터셉터 ID의 철자를 잘못 입력하는 것을 방지할 수 있습니다.

<br>

## References to Other Beans (Collaborators)

The ref element is the final element inside a <constructor-arg/> or <property/> definition element. Here, you set the
value of the specified property of a bean to be a reference to another bean (a collaborator) managed by the container.
The referenced bean is a dependency of the bean whose property is to be set, and it is initialized on demand as needed
before the property is set. (If the collaborator is a singleton bean, it may already be initialized by the container.)
All references are ultimately a reference to another object. Scoping and validation depend on whether you specify the ID
or name of the other object through the bean or parent attribute.

> `ref` 요소는 `<constructor-arg/>` 또는 `<property/>` 정의 요소 내부의 마지막 요소입니다. 여기에서는, 빈의 지정된 속성 값을 컨테이너에서 관리하는 다른 빈(콜라보레이터)에 대한
> 참조로 설정합니다. 참조된 빈은 속성을 설정하려는 빈의 의존성이며, 속성이 설정되기 전에 필요에 따라 초기화됩니다. (콜라보레이터가 싱글톤 빈인 경우, 컨테이너에 의해 이미 초기화되었을 수 있습니다.) 모든
> 참조는
> 궁극적으로 다른 객체에 대한 참조입니다. 범위 지정 및 유효성 검사는 `bean` 또는 `parent` 속성을 통해 다른 객체의 ID 또는 이름을 지정하는지의 여부에 따라 달라집니다.

Specifying the target bean through the bean attribute of the <ref/> tag is the most general form and allows creation of
a reference to any bean in the same container or parent container, regardless of whether it is in the same XML file. The
value of the bean attribute may be the same as the id attribute of the target bean or be the same as one of the values
in the name attribute of the target bean. The following example shows how to use a ref element:

> `<ref/>` 태그의 빈 속성을 통해 대상 빈을 지정하는 것이 가장 일반적인 형식이며, 동일한 XML 파일에 있는지의 여부에 관계없이 동일한 컨테이너 또는 상위 컨테이너에 있는 모든 빈에 대한 참조를 생성할 수
> 있습니다. 빈 속성의 값은 대상 빈의 `id` 속성과 동일하거나 대상 빈의 `name` 속성에 있는 값 중 하나와 동일할 수 있습니다. 아래의 예는 `ref` 요소를 사용하는 방법을 보여줍니다:

```xml
<ref bean="someBean"/>
```

<br>

Specifying the target bean through the parent attribute creates a reference to a bean that is in a parent container of
the current container. The value of the parent attribute may be the same as either the id attribute of the target bean
or one of the values in the name attribute of the target bean. The target bean must be in a parent container of the
current one. You should use this bean reference variant mainly when you have a hierarchy of containers and you want to
wrap an existing bean in a parent container with a proxy that has the same name as the parent bean. The following pair
of listings shows how to use the parent attribute:

> `parent` 속성을 통해 대상 빈을 지정하면, 현재 컨테이너의 상위 컨테이너에 있는 빈에 대한 참조가 생성됩니다. `parent` 속성의 값은 대상 빈의 id 속성 또는 대상 빈의 `name` 속성에 있는 값
> 중 하나와 동일할 수 있습니다. 대상 빈은 현재 컨테이너의 상위 컨테이너에 있어야 합니다. 이 빈의 참조 변형은 주로 컨테이너 계층 구조가 있고 부모 빈과 이름이 같은 프록시를 사용하여 기존 빈을 부모 컨테이너에
> 래핑하려는 경우에 사용해야 합니다. 아래의 목록은 `parent` 속성을 사용하는 방법을 보여줍니다:

```xml
<!-- in the parent context -->
<bean id="accountService" class="com.something.SimpleAccountService">
	<!-- insert dependencies as required here -->
</bean>
```

```xml
<!-- in the child (descendant) context -->
<bean id="accountService" <!-- bean name is the same as the parent bean -->
	class="org.springframework.aop.framework.ProxyFactoryBean">
	<property name="target">
		<ref parent="accountService"/> <!-- notice how we refer to the parent bean -->
	</property>
	<!-- insert other configuration and dependencies as required here -->
</bean>
```

<br>

The local attribute on the ref element is no longer supported in the 4.0 beans XSD, since it does not provide value over
a regular bean reference any more. Change your existing ref local references to ref bean when upgrading to the 4.0
schema.
{: .notice--primary}

> `ref` 요소의 `local` 속성은 더 이상 일반 `bean` 참조에 대한 값을 제공하지 않으므로, 4.0 bean XSD에서 더 이상 지원되지 않습니다. 4.0 스키마로 업그레이드할 때,
> 기존 `ref local` 참조를 `ref bean`으로 변경하세요.

<br>

## Inner Beans

A <bean/> element inside the <property/> or <constructor-arg/> elements defines an inner bean, as the following example
shows:

> 아래의 예제에서 볼 수 있듯이, `<property/>` 또는 `<constructor-arg/>` 요소 내부의 `<bean/>` 요소는 내부 빈을 정의합니다:

```xml
<bean id="outer" class="...">
	<!-- instead of using a reference to a target bean, simply define the target bean inline -->
	<!-- 대상 빈에 대한 참조를 사용하는 대신, 대상 빈을 한줄로 정의하기만 하면 됨 -->
	<property name="target">
		<bean class="com.example.Person"> <!-- this is the inner bean -->
			<property name="name" value="Fiona Apple"/>
			<property name="age" value="25"/>
		</bean>
	</property>
</bean>
```

An inner bean definition does not require a defined ID or name. If specified, the container does not use such a value as
an identifier. The container also ignores the scope flag on creation, because inner beans are always anonymous and are
always created with the outer bean. It is not possible to access inner beans independently or to inject them into
collaborating beans other than into the enclosing bean.

> 내부 빈 정의에는 정의된 ID나 이름이 필요하지 않습니다. 지정된 경우, 컨테이너는 이러한 값을 식별자로 사용하지 않습니다. 또한 내부 빈은 항상 익명이며, 항상 외부 빈과 함께 생성되므로 컨테이너는 생성
> 시 `scope` 플래그를 무시합니다. 내부 빈에 독립적으로 접근하거나 둘러싸는 빈이 아닌, 다른 협업 빈에 내부 빈을 삽입할 수 없습니다.

<br>

As a corner case, it is possible to receive destruction callbacks from a custom scope — for example, for a
request-scoped inner bean contained within a singleton bean. The creation of the inner bean instance is tied to its
containing bean, but destruction callbacks let it participate in the request scope’s lifecycle. This is not a common
scenario. Inner beans typically simply share their containing bean’s scope.

> 코너 케이스로서, 예를 들어 싱글톤 빈 내에 포함된 요청 범위 내부 빈에 대해 사용자 정의 범위에서 소멸 콜백을 수신할 수 있습니다. 내부 빈 인스턴스의 생성은 포함된 빈에 연결되지만, 소멸 콜백을 통해 요청
> 범위의 라이프사이클에 참여할 수 있습니다. 이것은 일반적인 시나리오는 아닙니다. 내부 빈은 일반적으로 단순하게 포함하는 빈의 범위를 공유합니다.

<br>

# Collections

The <list/>, <set/>, <map/>, and <props/> elements set the properties and arguments of the Java Collection types List,
Set, Map, and Properties, respectively. The following example shows how to use them:

> `<list/>`, `<set/>`, `<map/>` 및 `<props/>` 요소는 각각 자바 `Collection` 유형 `List`, `Set`, `Map` 및 `Preperties`의 속성과 인수를
> 설정합니다. 아래의 예제는 이 요소들의 사용 방법을 보여 줍니다:

```xml
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- OO의 호출 결과 -->
    
	<!-- results in a setAdminEmails(java.util.Properties) call -->
	<property name="adminEmails">
		<props>
			<prop key="administrator">administrator@example.org</prop>
			<prop key="support">support@example.org</prop>
			<prop key="development">development@example.org</prop>
		</props>
	</property>
	<!-- results in a setSomeList(java.util.List) call -->
	<property name="someList">
		<list>
			<value>a list element followed by a reference</value>
			<ref bean="myDataSource" />
		</list>
	</property>
	<!-- results in a setSomeMap(java.util.Map) call -->
	<property name="someMap">
		<map>
			<entry key="an entry" value="just some string"/>
			<entry key="a ref" value-ref="myDataSource"/>
		</map>
	</property>
	<!-- results in a setSomeSet(java.util.Set) call -->
	<property name="someSet">
		<set>
			<value>just some string</value>
			<ref bean="myDataSource" />
		</set>
	</property>
</bean>
```

<br>

The value of a map key or value, or a set value, can also be any of the following elements:

> 맵의 `key` 또는 `value`의 값 또는 `set`의 값은 아래의 요소 중 하나라도 될 수 있습니다:

```xml
bean | ref | idref | list | set | map | props | value | null
```

## Collection Merging

The Spring container also supports merging collections. An application developer can define a
parent <list/>, <map/>, <set/> or <props/> element and have child <list/>, <map/>, <set/> or <props/> elements inherit
and override values from the parent collection. That is, the child collection’s values are the result of merging the
elements of the parent and child collections, with the child’s collection elements overriding values specified in the
parent collection.

> 스프링 컨테이너는 컬렉션 병합도 지원합니다. 애플리케이션 개발자는 부모 `<list/>`, `<map/>`, `<set/>` 또는 `<props/>` 요소를 정의하고
> 자식 `<list/>`, `<map/>`, `<set/>` 또는 `<props/>` 요소가 부모 컬렉션의 값을 상속 및 재정의하도록 할 수 있습니다. 즉, 자식 컬렉션의 값은 부모 컬렉션과 자식 컬렉션의 요소를
> 병합한 결과이며, 자식 컬렉션의 컬렉션 요소가 부모 컬렉션에 지정된 값을 재정의합니다.

<br>

This section on merging discusses the parent-child bean mechanism. Readers unfamiliar with parent and child bean
definitions may wish to read the relevant section before continuing.

> 병합에 대한 이 섹션에서는 부모-자식 빈 메커니즘에 대해 설명합니다. 부모 및 자식 빈 정의에 익숙하지 않은 우리는 계속하기 전에 관련 섹션을 읽어 보란다.

<br>

The following example demonstrates collection merging:

> 아래의 예는 컬렉션 병합을 보여줍니다:

```xml
<beans>
	<bean id="parent" abstract="true" class="example.ComplexObject">
		<property name="adminEmails">
			<props>
				<prop key="administrator">administrator@example.com</prop>
				<prop key="support">support@example.com</prop>
			</props>
		</property>
	</bean>
	<bean id="child" parent="parent">
		<property name="adminEmails">
			<!-- the merge is specified on the child collection definition -->
			<!-- 병합은 자식 컬렉션 정의에 지정됩니다 -->
			<props merge="true">
				<prop key="sales">sales@example.com</prop>
				<prop key="support">support@example.co.uk</prop>
			</props>
		</property>
	</bean>
<beans>
```

<br>

Notice the use of the merge=true attribute on the <props/> element of the adminEmails property of the child bean
definition. When the child bean is resolved and instantiated by the container, the resulting instance has an adminEmails
Properties collection that contains the result of merging the child’s adminEmails collection with the parent’s
adminEmails collection. The following listing shows the result:

> 자식 빈 정의의 `adminEmails` 속성의 `<props/>` 요소에 `merge=true` 속성이 사용된 것을 주목하세요. `child` 빈이 컨테이너에 의해 확인되고 인스턴스화되면, 결과
> 인스턴스에는 `child`의 `adminEmails` 컬렉션을 부모의 `adminEmails` 컬렉션과 병합한 결과가 포함된 `adminEmails` `Properties` 컬렉션을 가집니다. 아래의 목록은
> 결과를
> 보여줍니다:

<br>

```
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```

The child Properties collection’s value set inherits all property elements from the parent <props/>, and the child’s
value for the support value overrides the value in the parent collection.

> 자식 `Preperties` 컬렉션의 값 집합은 상위 `<preps/>`에서 모든 속성 요소를 상속하며, `support` 값에 대한 하위의 값은 부모 컬렉션의 값을 재정의합니다.

<br>

This merging behavior applies similarly to the <list/>, <map/>, and <set/> collection types. In the specific case of
the <list/> element, the semantics associated with the List collection type (that is, the notion of an ordered
collection of values) is maintained. The parent’s values precede all of the child list’s values. In the case of the Map,
Set, and Properties collection types, no ordering exists. Hence, no ordering semantics are in effect for the collection
types that underlie the associated Map, Set, and Properties implementation types that the container uses internally.

> 이 병합 동작은 `<list/>`, `<map/>`, `<set/>` 컬렉션 유형에도 유사하게 적용됩니다. `<list/>` 요소의 특정 케이스에는 `List` 컬렉션 유형과 관련된 의미론(즉,
> 값의 `orderd` 컬렉션 개념인 듯?)이 유지됩니다. 부모 목록의 값은 모든 자식 목록의 값보다 앞에 위치합니다. `Map`, `Set` 그리고 `Preperties` 컬렉션 유형의 경우 순서가 존재하지
> 않습니다. 따라서 컨테이너가 내부적으로 사용하는 관련 `Map`, `Set` 및 `Preperties` 구현 유형의 기반이 되는 컬렉션 유형에는 순서 지정 의미론(semantics)이 적용되지 않습니다.

<br>

## Limitations of Collection Merging

You cannot merge different collection types (such as a Map and a List). If you do attempt to do so, an appropriate
Exception is thrown. The merge attribute must be specified on the lower, inherited, child definition. Specifying the
merge attribute on a parent collection definition is redundant and does not result in the desired merging.

> 서로 다른 컬렉션 유형(예: `Map`과 `List`)은 병합할 수 없습니다. 병합을 시도하면, 적절한 `Exception`이 발생합니다. `merge` 속성은 상속된 자식 정의에 지정해야 합니다. 부모 컬렉션
> 정의에 병합 속성을 지정하는 것은 중복되며, 원하는 병합이 이루어지지 않습니다.

<br>

## Strongly-typed collection

Thanks to Java’s support for generic types, you can use strongly typed collections. That is, it is possible to declare a
Collection type such that it can only contain (for example) String elements. If you use Spring to dependency-inject a
strongly-typed Collection into a bean, you can take advantage of Spring’s type-conversion support such that the elements
of your strongly-typed Collection instances are converted to the appropriate type prior to being added to the
Collection. The following Java class and bean definition show how to do so:

> 자바의 일반 유형의 지원 덕분에, 강력하게 유형화된 컬렉션을 사용할 수 있습니다. 즉, (예를 들어) `String` 요소만 포함할 수 있도록 `Collection` 유형을 선언할 수 있습니다. 스프링을 사용하여
> 강력하게 유형화된 `Collection`을 빈에 의존적으로 주입하는 경우, 강력하게 유형화된 `Collection` 인스턴스 요소가 `Collection`에 추가되기 전에 적절한 유형으로 반환되도록 스프링의 유형
> 변환 지원을 활용할 수 있습니다. 아래의 자바 클래스와 빈 정의는 이를 수행하는 방법을 보여줍니다:

```java
public class SomeClass {

	private Map<String, Float> accounts;

	public void setAccounts(Map<String, Float> accounts) {
		this.accounts = accounts;
	}
}
```

```xml
<beans>
	<bean id="something" class="x.y.SomeClass">
		<property name="accounts">
			<map>
				<entry key="one" value="9.99"/>
				<entry key="two" value="2.75"/>
				<entry key="six" value="3.99"/>
			</map>
		</property>
	</bean>
</beans>
```

When the accounts property of the something bean is prepared for injection, the generics information about the element
type of the strongly-typed Map<String, Float> is available by reflection. Thus, Spring’s type conversion infrastructure
recognizes the various value elements as being of type Float, and the string values (9.99, 2.75, and 3.99) are converted
into an actual Float type.

> `something` 빈의 `accounts` 속성이 주입을 위해 준비되면, 강력하게 유형화된 `Map<String, Float>`의 요소 유형에 대한 제네릭 정보를 반영하여 사용할 수 있습니다. 따라서 스프링의
> 유형 변환 인프라는 다양한 값 요소를 `Float` 유형으로 인식하고, 문자열 값(9.99, 2.75 및 3.99)을 실제 `Float` 유형으로 반환합니다.

<br>

## Null and Empty String Values

다음 이 시간에..