# Dependencies

A typical enterprise application does not consist of a single object (or bean in the Spring parlance). Even the simplest
application has a few objects that work together to present what the end-user sees as a coherent application. This next
section explains how you go from defining a number of bean definitions that stand alone to a fully realized application
where objects collaborate to achieve a goal.

> 일반적인 엔터프라이즈 애플리케이션은 단일 객체(또는 스프링 용어로 빈)로 구성되지 않습니다. 가장 단순한 애플리케이션조차도 최종 사용자에게 일관된 애플리케이션으로 보이기 위해 함께 작동하는 몇 가지 객체가
> 있습니다. 다음 섹션에서는 독립적으로 존재하는 여러 개의 빈 정의를 정의하는 것부터 객체들이 협업하여 목표를 달성하는 완전히 실현된 애플리케이션으로 전환하는 방법에 대해 설명합니다.

<br>

# Section Summary

- Dependency Injection
- Dependencies and Configuration in Detail
- Using depends-on
- Lazy-initialized Beans
- Autowiring Collaborators
- Method Injection