# Daily code snippets

**하루 30분! 공식 문서의 코드 조각을 읽고 정리하기**

1. 공식 문서를 보는 습관
2. 사용하는 프로그래밍 기술에 대한 이해
3. 영어 단어 및 독해 공부

### 1. Java

[Java Documentation](https://docs.oracle.com/en/java/javase/index.html)

### 2. Spring

[Spring Documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/)

### 3. AssertJ

[AssertJ Documentation](https://assertj.github.io/doc/)

### 4. MySQL

[MySQL Documentation](https://dev.mysql.com/doc/)

### 5. Lombok

[Lombok Documentation](https://projectlombok.org/features/)

### 6. HTTP 1.1

[RFC 2616 Documentation](https://www.rfc-editor.org/rfc/rfc2616)


### 7. Docker

[Docker Guide Documentation](https://docs.docker.com/get-started/overview/)

### 8. Kafka

[Kafka Guide Documentation](https://kafka.apache.org/documentation/#introduction)

### 9. Spring Data JDBC
[Spring Data JDBC Documentation](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#get-started:first-steps:spring)

## List

- [Java README.md](https://github.com/yeonise/daily-code-snippets/blob/main/Java/Java.md)
- [Spring README.md](https://github.com/yeonise/daily-code-snippets/blob/main/Spring/Spring.md)
- [AssertJ README.md](https://github.com/yeonise/daily-code-snippets/blob/main/AssertJ/AssertJ.md)
- [MySQL README.md](https://github.com/yeonise/daily-code-snippets/blob/main/MySQL/MySQL.md)
- [Lombok README.md](https://github.com/yeonise/daily-code-snippets/blob/main/Lombok/Lombok.md)
- [Docker README.md](Docker/README.md)
- [Kafka README.md](https://github.com/yeonise/daily-code-snippets/blob/main/kafka/Kafka.md)

## Participants

1. [@Sully](https://github.com/won4885)
2. [@Hyun](https://github.com/ghkdgus29)
3. [@Seoyeon](https://github.com/yeonise)
4. [@othertkfka](https://github.com/othertkfka)
4. [@YongHwan Kim](https://github.com/yonghwankim-dev)

## Guideline

1. 작성하는 문서 한 개는 **공식 문서의 한 페이지와 동일**합니다.  
   따라서, 공식 문서 한 페이지에 담긴 내용과 Markdown으로 작성한 문서의 내용은 같습니다.  
   다른 페이지의 내용이라면 **새 문서**에 작성합니다.

2. 영문 글과 독해는 문단으로 묶어서 반복합니다.  
   영문 글은 **일반 텍스트**로, 독해는 **인용문**으로 작성합니다.

Spring makes it easy to create Java enterprise applications. It provides everything you need to
embrace the Java language in an enterprise environment, with support for Groovy and Kotlin as
alternative languages on the JVM, and with the flexibility to create many kinds of architectures
depending on an application’s needs. As of Spring Framework 6.0, Spring requires Java 17+.
>
> _해당 문단에 대한 독해 내용_
>
Spring supports a wide range of application scenarios. In a large enterprise, applications often
exist for a long time and have to run on a JDK and application server whose upgrade cycle is beyond
developer control. Others may run as a single jar with the server embedded, possibly in a cloud
environment. Yet others may be standalone applications (such as batch or integration workloads) that
do not need a server.
>
> _해당 문단에 대한 독해 내용_

3. 코드가 있다면 ` ``` ``` ` 안에 작성합니다.  
   코드에 대한 추가 설명은 선택 사항입니다.

``` java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```
