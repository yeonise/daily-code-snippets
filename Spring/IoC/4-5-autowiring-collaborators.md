# Autowiring Collaborators

The Spring container can autowire relationships between collaborating beans. You can let Spring resolve collaborators (
other beans) automatically for your bean by inspecting the contents of the ApplicationContext. Autowiring has the
following advantages:

> 스프링 컨테이너는 협업하는 빈 간의 관계를 `autowire` 할 수 있습니다. `ApplicationContext`의 내용을 검사하여 스프링이 사용자의 빈에 대해 `collaborators`(다른 빈)를
> 자동으로 해결하도록 할 수 있습니다. `autowiring`에는 아래와 같은 이점이 있습니다:

- Autowiring can significantly reduce the need to specify properties or constructor arguments. (Other mechanisms such as
  a bean template discussed elsewhere in this chapter are also valuable in this regard.)
- Autowiring can update a configuration as your objects evolve. For example, if you need to add a dependency to a class,
  that dependency can be satisfied automatically without you needing to modify the configuration. Thus autowiring can be
  especially useful during development, without negating the option of switching to explicit wiring when the code base
  becomes more stable.

> - `autowiring`을 사용하여 속성이나 생성자 인수를 지정할 필요성을 크게 줄일 수 있습니다. (이 섹션의 다른 곳에서 설명하는 bean 템플릿과 같은 다른 메커니즘도 이와 관련하여 유용합니다.)
> - `autowiring`은 객체가 발달함에 따라 구성을 업데이트할 수 있습니다. 예를 들어 클래스에 의존성을 추가해야 하는 경우, 구성을 수정할 필요 없이 해당 의존성을 자동으로 충족할 수 있습니다.
    따라서 `autowiring`은 코드 기반이 안정화되면 분명한 `wiring`을 스위칭하는 옵션을 무효화하지 않고, 특히 개발 중에 유용할 수 있습니다.

<br>

When using XML-based configuration metadata (see Dependency Injection), you can specify the autowire mode for a bean
definition with the autowire attribute of the <bean/> element. The autowiring functionality has four modes. You specify
autowiring per bean and can thus choose which ones to autowire. The following table describes the four autowiring modes:

> XML-기반 구성 메타데이터를 사용하는 경우(의존성 주입 참조), `<bean/>` 요소의 `autowire` 속성을 사용하여 bean 정의에 대한 `autowire` 모드를 지정할 수
> 있습니다. `autowiring` 기능에는 네 가지 모드가 있습니다. `bean`별로 `autowiring`를 지정하여 어떤 `bean`을 `autowire`할지 선택할 수 있습니다. 아래의 표에서는 네
> 가지 `autowiring` 모드에 대해 설명합니다.

다음 시간에..