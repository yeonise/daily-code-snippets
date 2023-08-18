# Preface
The Spring Data JDBC project applies core Spring concepts to the development of solutions that use JDBC databases aligned with Domain-driven design principles.
We provide a “template” as a high-level abstraction for storing and querying aggregates.

This document is the reference guide for Spring Data JDBC Support.
It explains the concepts and semantics and syntax.

This section provides some basic introduction.
The rest of the document refers only to Spring Data JDBC features and assumes the user is familiar with SQL and Spring concepts.

> Spring Data JDBC 프로젝트는 스프링의 핵심 개념을 도메인 기반 설계 원칙 (DDD) 에 적용합니다. 
> 이 프로젝트는, 데이터 집합을 저장하고 가져오는 동작을 추상화하여 템플릿으로 제공합니다. 
> <br>
> 이 문서는 Spring Data JDBC 의 참조 가이드 입니다.
> 이 문서는 개념, 의미와 구문을 설명합니다.
> <br>
> 이 섹션에서는 몇 가지 기본적인 소개를 제공합니다.
> 문서의 나머지 부분에서는 Spring Data JDBC 기능만을 소개합니다.
> 사용자는 SQL 및 Spring 개념에 익숙하다고 가정합니다.

<br>

# 1. Learning Spring

Spring Data uses Spring framework’s core functionality, including:
- IoC container
- type conversion system
- expression language
- JMX integration
- DAO exception hierarchy.

While you need not know the Spring APIs, understanding the concepts behind them is important.
At a minimum, the idea behind Inversion of Control (IoC) should be familiar, and you should be familiar with whatever IoC container you choose to use.

> Spring Data 는 스프릥 프레임워크의 핵심 기능을 사용합니다. 
> 핵심 기능은 다음과 같습니다.
> - IoC 컨테이너 
> - 타입 변환 시스템 
> - 표현 언어 
> - JMX 통합 
> - DAO 예외 계층 
>
> 스프링 API를 알 필요는 없지만, 해당 개념들을 이해하는 것은 중요합니다. 
> 최소한 IoC 컨테이너의 기본개념과 사용에 익숙해야 합니다. 

<br>

The core functionality of the JDBC Aggregate support can be used directly, with no need to invoke the IoC services of the Spring Container.
This is much like JdbcTemplate, which can be used "'standalone'" without any other services of the Spring container.
To leverage all the features of Spring Data JDBC, such as the repository support, you need to configure some parts of the library to use Spring.

> JDBC Aggregate support 의 핵심기능은 스프링 컨테이너의 IoC 서비스를 호출할 필요없이 바로 사용할 수도 있습니다. 
> 이것은 스프링 컨테이너의 다른 서비스 사용 없이 "독립적" 으로 사용할 수 있는 JdbcTemplate 과 유사합니다.
> Spring Data JDBC 의 모든 기능을 사용하려면 (저장소 지원과 같은), 스프링의 일부 기능을 사용하도록 라이브러리 설정을 해야합니다.

<br>

To learn more about Spring, you can refer to the comprehensive documentation that explains the Spring Framework in detail.
There are a lot of articles, blog entries, and books on the subject.
See the Spring framework home page for more information.

> 스프링에 대해 더 학습하려면, 스프링 프레임워크를 설명하는 포괄적인 문서를 참고하십시오.
> 스프링을 다루는 많은 기사, 블로그와 책이 있습니다. 
> 더 많은 정보는 홈페이지를 참고하십시오.