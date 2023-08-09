# 8. Working with Spring Data Repositories

> 스프링 데이터 리포지토리 사용법

<br>

The goal of the Spring Data repository abstraction is to significantly reduce the amount of boilerplate code required to implement data access layers for various persistence stores.

> Spring Data 리포지토리 추상화의 목표는, 데이터 접근 계층에서 다양한 DB에 접근하기 위해 필요한 상용구 코드들을 상당수 제거하는 것입니다.

<br>

**Spring Data repository documentation and your module**

This chapter explains the core concepts and interfaces of Spring Data repositories.
The information in this chapter is pulled from the Spring Data Commons module.
It uses the configuration and code samples for the Jakarta Persistence API (JPA) module.
“Repository query keywords” covers the query method keywords supported by the repository abstraction in general.
For detailed information on the specific features of your module, see the chapter on that module of this document.

> **Spring Data 리포지토리 문서 및 모듈**
>
> 이 챕터에서 Spring Data 리포지토리의 핵심 개념과 인터페이스에 대해 설명합니다.
> 이 장의 정보는 Spring Data Commons 모듈에서 가져왔습니다.
> 이 정보들은 JPA 모듈에서 사용되는 구성 및 예제 코드들을 사용합니다.
> "리포지토리 쿼리 키워드" 는 일반적인 리포지토리 추상화에서 지원하는 쿼리 메서드 키워드를 다룹니다.
> 특정 모듈에 대한 더욱 자세한 정보를 원한다면, 특정 모듈에 대해 설명하는 이 문서에 해당하는 장을 찾아 참고하십시오.

<br>

## 8.1. Core concepts

> 핵심 컨셉

<br>

The central interface in the Spring Data repository abstraction is Repository.
It takes the domain class to manage as well as the identifier type of the domain class as type arguments.
This interface acts primarily as a marker interface to capture the types to work with and to help you to discover interfaces that extend this one.
The CrudRepository and ListCrudRepository interfaces provide sophisticated CRUD functionality for the entity class that is being managed.

Example 3. CrudRepository Interface

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {

    <S extends T> S save(S entity);      (1)

    Optional<T> findById(ID primaryKey); (2)

    Iterable<T> findAll();               (3)

    long count();                        (4)

    void delete(T entity);               (5)

    boolean existsById(ID primaryKey);   (6)

    // … more functionality omitted.
}
```

1. Saves the given entity.
2. Returns the entity identified by the given ID.
3. Returns all entities.
4. Returns the number of entities.
5. Deletes the given entity.
6. Indicates whether an entity with the given ID exists.

The methods declared in this interface are commonly referred to as CRUD methods.
`ListCrudRepository` offers equivalent methods, but they return List where the CrudRepository methods return an Iterable.

> Spring Data 리포지토리 추상화의 핵심 인터페이스는 리포지토리입니다.
> 관리할 도메인 클래스와 도메인 클래스의 식별자 타입을 타입 변수로 사용합니다. (`<클래스, 식별자>`)
> 이 인터페이스는 마커 인터페이스로서 사용할 클래스 타입과 해당 인터페이스를 확장한 다른 인터페이스들을 찾는데 도움을 줍니다.
> `CrudRepository` 와 `ListCrudRepository` 인터페이스는 엔티티를 다루기 위한 정교한 CRUD 기능을 제공합니다.
>
> 예제 3. `CrudRepository` 인터페이스
> (코드 생략)
> 1. 주어진 엔티티를 저장합니다.
> 2. 주어진 ID를 갖는 엔티티를 반환합니다.
> 3. 모든 엔티티들을 반환합니다.
> 4. 엔티티의 개수를 반환합니다.
> 5. 주어진 엔티티를 삭제합니다.
> 6. 주어진 ID를 갖는 엔티티가 존재하는지 검사합니다.
>
> 이 인터페이스에 선언된 메서드는 일반적으로 CRUD 메서드로 불립니다.
> `ListCrudRepository` 는 똑같은 메서드들을 제공하지만, `Iterable` 대신 `List`를 반환합니다.

<br>

**Note**
We also provide persistence technology-specific abstractions, such as `JpaRepository` or `MongoRepository`.
Those interfaces extend CrudRepository and expose the capabilities of the underlying persistence technology in addition to the rather generic persistence technology-agnostic interfaces such as CrudRepository.

> 우리는 특정 기술에 맞춰진 추상화를 제공하기도 합니다. (`JpaRepository` 나 `MongoRepository` 같은)
> 이러한 인터페이스들은 `CrudRepository` 를 상속합니다.
> 이러한 인터페이스들은 일반적인 추상화인 `CrudRepository` 기능뿐 아니라, 특정 기술의 기능들을 노출합니다.

<br>

Additional to the CrudRepository, there is a PagingAndSortingRepository abstraction that adds additional methods to ease paginated access to entities:

Example 4. PagingAndSortingRepository interface

```java
public interface PagingAndSortingRepository<T, ID> {

    Iterable<T> findAll(Sort sort);

    Page<T> findAll(Pageable pageable);
}
```

To access the second page of User by a page size of 20, you could do something like the following:

```java
PagingAndSortingRepository<User, Long> repository= // … get access to a bean
        Page<User> users=repository.findAll(PageRequest.of(1,20));
```

In addition to query methods, query derivation for both count and delete queries is available.
The following list shows the interface definition for a derived count query:

Example 5. Derived Count Query

```java
interface UserRepository extends CrudRepository<User, Long> {

    long countByLastname(String lastname);
}
```

The following listing shows the interface definition for a derived delete query:

Example 6. Derived Delete Query

```java
interface UserRepository extends CrudRepository<User, Long> {

    long deleteByLastname(String lastname);

    List<User> removeByLastname(String lastname);
}
```

> `CrudRepository` 외에도, 엔티티에 대한 페이지네이션을 쉽게 도와주는 추상화인 `PagingAndSortingRepository` 가 있습니다.
>
> 예제 4. `PagingAndSortingRepository` 인터페이스
> (코드 생략)
>
> User 객체로 이루어진 한 페이지 사이즈가 20이고, 두 번째 페이지에 접근하는 경우, 이렇게 코드를 사용할 수 있습니다.
> (코드 생략)
>
> 쿼리 메서드 외에도, 개수 및 삭제에 대한 쿼리들을 파생할 수 있습니다.
> 다음 예제는 특정 조건으로 정의한 파생된 카운트 쿼리입니다.
>
> 예제5. 파생된 카운트 쿼리
> (코드 생략)
>
> 다음 예제는 사용자가 정의한 파생된 삭제 쿼리입니다.
>
> 예제6. 파생된 삭제 쿼리
> (코드 생략)

<br>

## 8.2. Query Methods

Standard CRUD functionality repositories usually have queries on the underlying datastore.
With Spring Data, declaring those queries becomes a four-step process:

1. Declare an interface extending Repository or one of its subinterfaces and type it to the domain class and ID type that it should handle, as shown in the following example:

```java
interface PersonRepository extends Repository<Person, Long> { …
}
```

2. Declare query methods on the interface.

```java
interface PersonRepository extends Repository<Person, Long> {
    List<Person> findByLastname(String lastname);
}
```

3. Set up Spring to create proxy instances for those interfaces, either with JavaConfig or with XML configuration.

```java
import org.springframework.data.….repository.config.EnableJpaRepositories;

@EnableJpaRepositories
class Config { …
}
```

Note that the JavaConfig variant does not configure a package explicitly, because the package of the annotated class is used by default.
To customize the package to scan, use one of the basePackage… attributes of the data-store-specific repository’s `@EnableJpaRepositories` annotation.

4. Inject the repository instance and use it, as shown in the following example:

```java
class SomeClient {

    private final PersonRepository repository;

    SomeClient(PersonRepository repository) {
        this.repository = repository;
    }

    void doSomething() {
        List<Person> persons = repository.findByLastname("Matthews");
    }
}
```

The sections that follow explain each step in detail:

- Defining Repository Interfaces
- Defining Query Methods
- Creating Repository Instances
- Custom Implementations for Spring Data Repositories

> 표준 CRUD 리포지토리는 데이터 저장소에 대한 기본적인 쿼리를 지원합니다.
> Spring Data 를 사용하면, 이러한 쿼리를 4단계에 거쳐 선언할 수 있습니다.
>
> 1. Repository 또는 이의 하위 인터페이스를 확장하는 인터페이스를 선언하고, 처리하려는 도메인 클래스와 식별자 타입을 다음과 같이 입력하십시오.
     > (코드 생략)
> 2. 쿼리 메서드를 인터페이스에 선언합니다.
     > (코드 생략)
> 3. JavaConfig 나 XML 설정을 통해 해당 인터페이스에 대한 프록시 인스턴스를 생성할 수 있도록 설정합니다.
     > (코드 생략)
     > 애노테이션이 달린 클래스의 패키지가 기본적으로 사용되기 때문에, JavaConfig 변형은 패키지를 명시적으로 설정하지 않습니다.
     > 스캔할 패키지를 변경하려면, basePackage 속성 중 하나인 `@EnableJpaRepositories` 애노테이션을 사용하십시오.
> 4. 다음 예제와 같이, 리포지토리를 주입받아 사용하십시오.
     > (코드 생략)
>
> 다음 섹션에서는 각 단계를 자세히 설명합니다.
> - 리포지토리 인터페이스 정의
> - 쿼리 메서드 정의
> - 리포지토리 인스턴스 생성
> - 사용자 정의 Spring Data 리포지토리 만들기

<br>

## 8.3. Defining Repository Interfaces

> 리포지토리 인터페이스 정의

<br>

To define a repository interface, you first need to define a domain class-specific repository interface.
The interface must extend Repository and be typed to the domain class and an ID type.
If you want to expose CRUD methods for that domain type, you may extend CrudRepository, or one of its variants instead of Repository.

> 리포지토리 인터페이스를 정의하기 위해, 먼저 도메인 클래스에 특화된 리포지토리 인터페이스를 정의해야 합니다.
> 인터페이스는 반드시 Repository 를 확장해야 하고, 도메인 클래스와 식별자 타입으로 타입이 지정됩니다.
> 만약 CRUD 메서드들을 사용하려는 경우, `Repository` 대신 `CrudRepository` 나 이것의 변형을 확장할 수 있습니다.

<br>

### 8.3.1. Fine-tuning Repository Definition

> 미세한 조정을 위한 리포지토리 정의

<br> 

There are a few variants how you can get started with your repository interface.

The typical approach is to extend CrudRepository, which gives you methods for CRUD functionality.
CRUD stands for Create, Read, Update, Delete.
With version 3.0 we also introduced ListCrudRepository which is very similar to the CrudRepository but for those methods that return multiple entities it returns a List instead of an Iterable which you might find easier to use.

If you are using a reactive store you might choose ReactiveCrudRepository, or RxJava3CrudRepository depending on which reactive framework you are using.

If you are using Kotlin you might pick CoroutineCrudRepository which utilizes Kotlin’s coroutines.

> 사용자 정의 리포지토리 인터페이스를 사용하는데 몇 가지 방법이 있습니다.
>
> 가장 일반적인 방법은 CRUD 기능을 제공하는 `CrudRepository` 를 확장하는 것입니다.
> CRUD 는 생성, 읽기, 수정, 삭제를 의미합니다.
> 3.0 버전 이후부터는 `Iterable` 대신 사용하기 쉬운 `List`를 반환하는 `ListCrudyRepository`를 지원합니다.
>
> 만약 반응형 저장소를 사용한다면 사용중인 반응형 프레임워크에 따라 `ReactiveCrudRpository` 또는 `RxJava3CrudRepository`를 사용할 수 있습니다.
>
> 만약 Kotlin을 사용하는 경우, Kotlin 을 활용하는 `CoroutineCrudRepository` 를 사용할 수 있습니다.

<br>

Additional you can extend PagingAndSortingRepository, ReactiveSortingRepository, RxJava3SortingRepository, or CoroutineSortingRepository if you need methods that allow to specify a Sort abstraction or in the first case a Pageable abstraction.
Note that the various sorting repositories no longer extended their respective CRUD repository as they did in Spring Data Versions pre 3.0.
Therefore, you need to extend both interfaces if you want functionality of both.

If you do not want to extend Spring Data interfaces, you can also annotate your repository interface with `@RepositoryDefinition`.
Extending one of the CRUD repository interfaces exposes a complete set of methods to manipulate your entities.
If you prefer to be selective about the methods being exposed, copy the methods you want to expose from the CRUD repository into your domain repository.
When doing so, you may change the return type of methods.
Spring Data will honor the return type if possible.
For example, for methods returning multiple entities you may choose Iterable<T>, List<T>, Collection<T> or a VAVR list.

> 정렬을 추상화한 메서드나, 페이징을 추상화한 메서드가 필요한 경우
> 추가적으로 `PaginAndSortingRepository`, `ReactiveSortingRepository`, `RxJava3SortingRepository`, `CoroutineSortingRepository`
> 를 확장할 수 있습니다.
> 정렬 리포지토리를 사용하고 싶을 때 3.0 이전 버전에서 처럼 리포지토리 각각의 CRUD 리포지토리 인터페이스를 확장하지 않습니다.
> 따라서, 두 가지 정렬 기능을 사용하고 싶다면, 두 인터페이스를 모두 확장해야 합니다.
>
> `Spring Data` 인터페이스를 확장하지 않으려면, 당신의 리포지토리 인터페이스에 `@RepositoryDefinition` 애노테이션을 붙여 사용할 수 있습니다.
> CRUD 리포지토리를 하나라도 확장하게 되면, 인터페이스는 엔티티를 조작하는 많은 메서드 집합에 노출됩니다.
> 만약 선택적으로 메서드를 노출하려면, CRUD 리포지토리에서 원하는 메서드만을 복사해 당신의 도메인 리포지토리에서 사용하십시오.
> 이때 메서드의 반환 타입을 변경할 수도 있습니다.
> `Spring Data` 는 호환 가능한 경우 다양한 반환 타입을 사용할 수 있습니다.
> 에를 들어 여러개의 엔티티를 반환하는 메서드의 반환타입을 `Iterable`, `List`, `Collection`, `VAVR 목록` 에서 고를 수 있습니다.

<br>

If many repositories in your application should have the same set of methods you can define your own base interface to inherit from.
Such an interface must be annotated with @NoRepositoryBean.
This prevents Spring Data to try to create an instance of it directly and failing because it can’t determine the entity for that repository, since it still contains a generic type variable.

The following example shows how to selectively expose CRUD methods (findById and save, in this case):

Example 7. Selectively exposing CRUD methods

```java

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends Repository<T, ID> {

    Optional<T> findById(ID id);

    <S extends T> S save(S entity);
}

interface UserRepository extends MyBaseRepository<User, Long> {
    User findByEmailAddress(EmailAddress emailAddress);
}
```

In the prior example, you defined a common base interface for all your domain repositories and exposed findById(…) as well as save(…).
These methods are routed into the base repository implementation of the store of your choice provided by Spring Data (for example, if you use JPA, the implementation is SimpleJpaRepository), because they match the method signatures in CrudRepository.
So the UserRepository can now save users, find individual users by ID, and trigger a query to find Users by email address.

**Note**
The intermediate repository interface is annotated with @NoRepositoryBean.
Make sure you add that annotation to all repository interfaces for which Spring Data should not create instances at runtime.

> 당신의 애플리케이션에서 많은 리포지토리들이 똑같은 메서드 집합이 필요한 경우, 당신만의 베이스 인터페이스를 만들고, 다른 리포지토리들이 상속하도록 할 수 있습니다.
> 이러한 인터페이스엔 반드시 `@NoRepositoryBean` 애노테이션을 붙여야 합니다.
> 이 애노테이션은 `Spring Data` 가 해당 인터페이스의 인스턴스를 생성하려고 시도하고, 실패하는 것 (해당 리포지토리 엔티티를 확인할 수 없기 때문에) 을 방지합니다.
> (왜냐하면 해당 인터페이스는 일반 타입 변수를 포함하기 때문에)
>
> 다음 예제는 어떻게 CRUD 메서드 중 일부만 선택적으로 노출하는지를 보여줍니다. (findById, save)
>
> 예제 7. 선택적으로 CRUD 메서드 사용
> (코드 생략)
>
> 앞선 예제에서는, 공통으로 사용할 당신만의 인터페이스를 정의하기 위해서, domain 리포지토리를 확장한 뒤, 사용할 특정 메서드들을 노출했습니다.
> 이러한 메서드들은 `CrudRepository` 와 메서드 시그니처가 같기 때문에, `Spring Data` 가 제공하는 기본 리포지토리 구현체로 라우팅됩니다. (예를들어, 만약 당신이 JPA를
> 사용용한다면, `SimpleJpaRepository` 구현체로 라우팅됩니다.)
> 그래서 `UserRepository` 는 이제 유저를 저장하고, id , email 을 이용해 개별 유저를 찾을 수 있습니다.
>
> **노트**
> 중간 매개체가 되는 리포지토리 인터페이스는 `@NoRepositoryBean` 애노테이션을 붙입니다.
> `Spring Data` 가 런타임에 인스턴스를 생성하면 안되는 리포지토리 인터페이스엔 해당 주석을 반드시 붙여야 합니다.

<br>

### 8.3.2. Using Repositories with Multiple Spring Data Modules

> 다양한 Spring Data 모듈과 함께 리포지토리 사용하기

<br>

Using a unique Spring Data module in your application makes things simple, because all repository interfaces in the defined scope are bound to the Spring Data module.
Sometimes, applications require using more than one Spring Data module.
In such cases, a repository definition must distinguish between persistence technologies.
When it detects multiple repository factories on the class path, Spring Data enters strict repository configuration mode.
Strict configuration uses details on the repository or the domain class to decide about Spring Data module binding for a repository definition:

1. If the repository definition extends the module-specific repository, it is a valid candidate for the particular Spring Data module.
2. If the domain class is annotated with the module-specific type annotation, it is a valid candidate for the particular Spring Data module.
   Spring Data modules accept either third-party annotations (such as JPA’s @Entity) or provide their own annotations (such as @Document for Spring Data MongoDB and Spring Data Elasticsearch).

> 하나의 Spring Data 모듈을 사용하면 작업이 간단해집니다.
> 왜냐하면 모든 리포지토리 인터페이스가 하나의 Spring Data 모듈에 의존하기 때문입니다.
> 그러나 때때로는, 애플리케이션은 하나 이상의 Spring Data 모듈을 사용해야 합니다.
> 이러한 경우, 리포지토리 정의는 DB 지속성 기술을 구분해야 합니다.
> 클래스 경로에서 여러 리포지토리 팩토리를 감지하면, Spring Data 는 엄격한 리포지토리 설정 모드로 들어갑니다.
> 엄격한 설정은 리포지토리 또는 도메인 클래스에 대한 세부정보를 사용하여 리포지토리 정의에알맞는 Spring Data 모듈을 결정합니다.
>
> 1. 만약 리포지토리 정의가 특정 모듈에만 특화된 리포지토리를 확장하는 경우, 특정 Spring Data 모듈을 사용할 후보입니다.
> 2. 만약 도메인 클래스에 특정 모듈에만 특화된 애노테이션이 붙어있는 경우, 특정 Spring Data 모듈을 사용할 후보입니다.
     > Spring Data 모듈은 third-party 애노테이션을 사용을 허용하거나, Spring Data 모듈의 애노테이션을 자체 제공합니다.

<br>

The following example shows a repository that uses module-specific interfaces (JPA in this case):
Example 8. Repository definitions using module-specific interfaces

```java
interface MyRepository extends JpaRepository<User, Long> {
}

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends JpaRepository<T, ID> { …
}

interface UserRepository extends MyBaseRepository<User, Long> { …
}
```

MyRepository and UserRepository extend JpaRepository in their type hierarchy.
They are valid candidates for the Spring Data JPA module.

> 다음의 예제들은 특정 모듈에 특화된 인터페이스들을 사용하는 리포지토리를 보여줍니다. (이 경우엔 JPA)
> 예제 8. 특정 모듈에 특화된 인터페이스들을 사용하는 리포지토리 정의들
> (코드 생략)
> `MyRepository` 와 `UserRepository` 는 `JpaRepository` 를 그들의 타입계층에서 확장합니다.
> 이들은 Spring Data JPA 모듈의 유효한 후보입니다.

<br>

The following example shows a repository that uses generic interfaces:
Example 9. Repository definitions using generic interfaces

```java
interface AmbiguousRepository extends Repository<User, Long> { …
}

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends CrudRepository<T, ID> { …
}

interface AmbiguousUserRepository extends MyBaseRepository<User, Long> { …
}
```

AmbiguousRepository and AmbiguousUserRepository extend only Repository and CrudRepository in their type hierarchy.
While this is fine when using a unique Spring Data module, multiple modules cannot distinguish to which particular Spring Data these repositories should be bound.

> 다음의 예제들은 일반 인터페이스들을 사용하는 리포지토리를 보여줍니다.
> 예제 9. 일반 인터페이스들을 사용하는 리포지토리 정의들
> (코드 생략)
> `AmbiguousRepository` 와 `AmbiguousUserRepository` 는 `Repository` 와 `CrudRepository` 만을 그들의 타입계층내에서 확장합니다.
> 하나의 Spring Data 모듈을 사용할 땐 문제가 되지 않지만, 여러개의 Spring Data 모듈들을 사용하는 경우 어떤 Spring Data 가 리포지토리에 바인딩되어야 하는 지 구분할 수 없습니다.

<br>

The following example shows a repository that uses domain classes with annotations:
Example 10. Repository definitions using domain classes with annotations

```java
interface PersonRepository extends Repository<Person, Long> { …
}

@Entity
class Person { …
}

interface UserRepository extends Repository<User, Long> { …
}

@Document
class User { …
}
```

PersonRepository references Person, which is annotated with the JPA @Entity annotation, so this repository clearly belongs to Spring Data JPA.
UserRepository references User, which is annotated with Spring Data MongoDB’s @Document annotation.

> 다음 예제는 애노테이션이 붙은 도메인 클래스 리포지토리를 보여줍니다.
> 예제 10) 애노테이션이 붙은 도메인 클래스를 사용하는 리포지토리 정의
> (코드 생략)
> `PersonRepository` 는 `@Entity` 애노테이션이 붙은 `Person` 클래스를 참조합니다.
> 그래서 이 리포지토리는 Spring Data JPA 에 종속됩니다.
> `UserRepository` 는 `MongoDB` 의 `@Document` 애노테이션이 붙은 `User` 클래스를 참조합니다.

<br>

The following bad example shows a repository that uses domain classes with mixed annotations:
Example 11. Repository definitions using domain classes with mixed annotations

```java
interface JpaPersonRepository extends Repository<Person, Long> { …
}

interface MongoDBPersonRepository extends Repository<Person, Long> { …
}

@Entity
@Document
class Person { …
}

```

This example shows a domain class using both JPA and Spring Data MongoDB annotations.
It defines two repositories, JpaPersonRepository and MongoDBPersonRepository.
One is intended for JPA and the other for MongoDB usage.
Spring Data is no longer able to tell the repositories apart, which leads to undefined behavior.

> 다음의 잘못된 예제는 애노테이션을 혼합한 도메인 클래스를 사용하는 리포지토리를 보여줍니다.
> 예제 11) 여러개의 애노테이션을 붙인 도메인 클래스를 사용하는 리포지토리 정의
> (코드 생략)
> 다음 예제는 `JPA` 와 `Spring Data MongoDB` 애노테이션을 모두 사용하는 도메인 클래스를 보여줍니다.
> 현재 `JpaPersonRepository` 와 `MongoDBPersonRepository` 를 정의하였습니다.
> 하나는 `JPA` 용이고, 다른 하나는 `MongoDB` 용 입니다.
> Spring Data 는 리포지토리를 구분할 수 없으므로, 정의되지 않은 동작이 발생합니다.

<br>

Repository type details and distinguishing domain class annotations are used for strict repository configuration to identify repository candidates for a particular Spring Data module.
Using multiple persistence technology-specific annotations on the same domain type is possible and enables reuse of domain types across multiple persistence technologies.
However, Spring Data can then no longer determine a unique module with which to bind the repository.

> 리포지토리 타입 세부정보와 구별되는 도메인 클래스용 애노테이션은 특정 Spring Data 모듈을 사용하는 리포지토리를 구분하기 위해 사용됩니다.
> 동일한 도메인 타입에서 다양한 persistence 기술의 애노테이션을 사용할 수 있고, 다양한 persistence 기술에서 동일한 도메인 타입을 재사용할 수 있습니다.
> 하지만 이 경우, Spring Data 는 더 이상 리포지토리에서 사용할 단일 모듈을 결정할 수 없습니다.

<br>

The last way to distinguish repositories is by scoping repository base packages.
Base packages define the starting points for scanning for repository interface definitions, which implies having repository definitions located in the appropriate packages.
By default, annotation-driven configuration uses the package of the configuration class.
The base package in XML-based configuration is mandatory.

The following example shows annotation-driven configuration of base packages:
Example 12. Annotation-driven configuration of base packages

```java

@EnableJpaRepositories(basePackages = "com.acme.repositories.jpa")
@EnableMongoRepositories(basePackages = "com.acme.repositories.mongo")
class Configuration { …
}
```

> 리포지토리를 식별하는 마지막 방법은 리포지토리의 기본 패키지 범위를 지정하는 것입니다.
> 기본 패키지는 리포지토리 인터페이스 정의의 스캔 시작점을 정의합니다.
> 이는 적절한 패키지에 적절한 리포지토리 정의가 있음을 의미합니다.
> 기본적으로, 애노테이션 기반 설정은 설정 클래스의 패키지를 기본 패키지 위치로 설정합니다.
> XML 기반 설정은 기본 패키지 위치 설정이 필수 입니다.
>
> 다음의 예제는 기본 패키지의 애노테이션 기반 설정을 보여줍니다.
> 예제 12) 기본 패키지의 애노테이션 기반 설정
> (코드 생략)

<br>

### 8.4. Defining Query Methods

> 쿼리 메서드 정의

<br>

The repository proxy has two ways to derive a store-specific query from the method name:

- By deriving the query from the method name directly.
- By using a manually defined query.
  Available options depend on the actual store.
  However, there must be a strategy that decides what actual query is created.
  The next section describes the available options.

> 리포지토리 프록시에는 메서드 이름을 사용하여 특정 쿼리를 만드는 두 가지 방법이 있습니다.
> - 메서드 이름에서 직접 쿼리를 만들기
> - 수동으로 직접 쿼리를 만들기
    > 사용가능한 옵션은 실제 저장된 데이터에 따라 다릅니다.
    > 하지만 실제 어떤 쿼리를 생성할지 결정하는 전략이 있어야 합니다.
    > 다음 섹션에서 사용가능한 옵션들을 설명합니다.

<br>

### 8.4.1. Query Lookup Strategies

> 쿼리 조회 전략

The following strategies are available for the repository infrastructure to resolve the query.
For Java configuration, you can use the queryLookupStrategy attribute of the EnableJpaRepositories annotation.
Some strategies may not be supported for particular datastores.

- CREATE attempts to construct a store-specific query from the query method name.
  The general approach is to remove a given set of well known prefixes from the method name and parse the rest of the method.
  You can read more about query construction in “Query Creation”.
- USE_DECLARED_QUERY tries to find a declared query and throws an exception if it cannot find one.
  The query can be defined by an annotation somewhere or declared by other means.
  See the documentation of the specific store to find available options for that store.
  If the repository infrastructure does not find a declared query for the method at bootstrap time, it fails.
- CREATE_IF_NOT_FOUND (the default) combines CREATE and USE_DECLARED_QUERY.
  It looks up a declared query first, and, if no declared query is found, it creates a custom method name-based query.
  This is the default lookup strategy and, thus, is used if you do not configure anything explicitly.
  It allows quick query definition by method names but also custom-tuning of these queries by introducing declared queries as needed.

> 리포지토리 환경에서 쿼리를 해결하기 위해 다음의 전략들을 사용할 수 있습니다.
> Java 설정의 경우, `EnableJpaRepositories` 애노테이션의 `queryLookupStrategy` 속성을 사용할 수 있습니다.
> 일부 전략은 특정 데이터베이스에선 지원되지 않습니다.
>
> - `CREATE` 는 쿼리 메서드 이름을 이용해 DB별로 특화된 쿼리를 만듭니다.
    > 일반적인 접근 방식은 메서드 이름에서 파생된 접두사 집합을 제거하고, 메서드의 나머지 부분을 분석합니다.
    > "Query Creation" 란에서 쿼리 생성에 관한 자세한 정보를 얻을 수 있습니다.
> - `USE_DECLARED_QUERY` 는 선언된 쿼리를 찾고, 만약 선언된 쿼리가 없으면 예외를 발생시킵니다.
    > 쿼리는 애노테이션으로 정의되거나 다른 방식으로 선언될 수 있습니다.
    > 특정 DB 문서를 읽으면서 적용가능한 옵션이 있는지 확인하십시오.
    > 만약 리포지토리 인프라가 부트스트랩 시간에 선언된 쿼리를 찾지 못한다면 실패합니다.
> - `CREATE_IF_NOT_FOUND` 는 `CREATE` 와 `USE_DECLARED_QUERY`를 결합한 옵션입니다.
    > 먼저 선언된 쿼리를 찾고, 선언된 쿼리가 없다면 메서드 이름 기반 쿼리를 만듭니다.
    > 이것은 쿼리 조회 전략의 기본 옵션으로, 명시적으로 설정하지 않으면 기본적으로 사용됩니다.
    > 이 전략에선, 메서드 이름 기반의 빠른 쿼리 정의와 사용자가 직접 만든 쿼리를 모두 사용할 수 있습니다.

<br>

### 8.4.2. Query Creation

> 쿼리 생성

The query builder mechanism built into the Spring Data repository infrastructure is useful for building constraining queries over entities of the repository.

The following example shows how to create a number of queries:
Example 13. Query creation from method names

```java
interface PersonRepository extends Repository<Person, Long> {

    List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

    // Enables the distinct flag for the query
    List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);

    List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

    // Enabling ignoring case for an individual property
    List<Person> findByLastnameIgnoreCase(String lastname);

    // Enabling ignoring case for all suitable properties
    List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

    // Enabling static ORDER BY for a query
    List<Person> findByLastnameOrderByFirstnameAsc(String lastname);

    List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
}

```

Parsing query method names is divided into subject and predicate.
The first part (find…By, exists…By) defines the subject of the query, the second part forms the predicate.
The introducing clause (subject) can contain further expressions.
Any text between find (or other introducing keywords) and By is considered to be descriptive unless using one of the result-limiting keywords such as a Distinct to set a distinct flag on the query to be created or Top/First to limit query results.

> Spring Data 리포지토리 인프라에 내장된 쿼리 생성 메커니즘은 엔티티에 대한 제약 쿼리를 빌드하는 데 유용합니다.
>
> 다음 예제들은 다양한 쿼리를 만드는 예제를 보여줍니다.
> 예제 13) 메서드 이름을 통한 쿼리 생성
> (코드 생략)
> 쿼리 메서드 명 분석은 목적어와 술어로 나뉩니다.
> `find By`, `exists..By` 와 같은 첫 번째 부분은 쿼리의 목적어이고, 두 번째 부분은 술어입니다.
> 소개하는 부분 (목적어)은 부연설명을 추가할 수 있습니다.
> `Distinct` 나 `Top/First`처럼 쿼리 결과에 영향을 주는 키워드만 아니라면 `find` 와 `By` 사이에 들어간 글자는 부연설명으로 취급됩니다.

<br>

The appendix contains the full list of query method subject keywords and query method predicate keywords including sorting and letter-casing modifiers.
However, the first By acts as a delimiter to indicate the start of the actual criteria predicate.
At a very basic level, you can define conditions on entity properties and concatenate them with And and Or.

The actual result of parsing the method depends on the persistence store for which you create the query.
However, there are some general things to notice:

- The expressions are usually property traversals combined with operators that can be concatenated.
  You can combine property expressions with AND and OR.
  You also get support for operators such as Between, LessThan, GreaterThan, and Like for the property expressions.
  The supported operators can vary by datastore, so consult the appropriate part of your reference documentation.
- The method parser supports setting an IgnoreCase flag for individual properties (for example,
  findByLastnameIgnoreCase(…)) or for all properties of a type that supports ignoring case (usually String instances — for example, findByLastnameAndFirstnameAllIgnoreCase(…)).
  Whether ignoring cases is supported may vary by store, so consult the relevant sections in the reference documentation for the store-specific query method.
- You can apply static ordering by appending an OrderBy clause to the query method that references a property and by providing a sorting direction (Asc or Desc).
  To create a query method that supports dynamic sorting, see “Paging, Iterating Large Results, Sorting”.

> 부록에는 정렬과 대소문자를 포함한 모든 쿼리 메서드 키워드들이 있습니다.
> 첫 번째 `By` 는 실제 술어 부분을 나누는 구분자 역할을 합니다.
> 기본적인 수준에서, 조건이나 엔티티 속성을 `And` 나 `Or` 로 연결할 수 있습니다.
>
> 메서드 구문 분석의 실제 결과는 당신이 사용하는 DB에 따라 다릅니다.
> 하지만, 몇 가지 일반적인 주의사항이 있습니다.
> - 일반적으로 표현식은 속성 탐색과 연산자의 조합입니다.
    > 당신은 `AND` 와 `OR` 를 사용해 속성을 결합할 수 있습니다.
    > 또한 `Between`, `LessThan`, `GreaterThan`, `Like` 와 같은 연산자를 사용할 수 있습니다.
    > 지원되는 연산자는 사용하는 DB에 따라 다르기 때문에, 참조 문서에 해당 부분을 참고하십시오.
> - 메서드 파서는 개별속성 또는 모든 속성의 대소문자를 무시하는 플래그를 지원합니다. (`findByLastnameIgnoreCase(...)`
    나 `findByLastnameAndFirstnameAllIgnoreCase(…)` 같은)
    > 해당 옵션의 지원 여부 역시 DB에 따라 다르므로, 참조 문서의 관련 섹션을 참고하십시오.
> - `OrderBy` 절을 추가하여 특정 속성에 대한 정적 정렬을 적용할 수 있고, `Asc`, `Desc` 절을 추가하여 정렬 방향을 설정할 수 있습니다.
    > 동적 정렬을 지원하는 쿼리 메서드를 생성하려면, "Paging, Iterating Large Results, Sorting" 섹션을 참고하십시오.

<br>

### 8.4.3. Property Expressions

> 속성 표현식

<br>

Property expressions can refer only to a direct property of the managed entity, as shown in the preceding example.
At query creation time, you already make sure that the parsed property is a property of the managed domain class.
However, you can also define constraints by traversing nested properties.
Consider the following method signature:

```java
List<Person> findByAddressZipCode(ZipCode zipCode);
```

Assume a Person has an Address with a ZipCode.
In that case, the method creates the x.address.zipCode property traversal.
The resolution algorithm starts by interpreting the entire part (AddressZipCode) as the property and checks the domain class for a property with that name (uncapitalized).
If the algorithm succeeds, it uses that property.
If not, the algorithm splits up the source at the camel-case parts from the right side into a head and a tail and tries to find the corresponding property — in our example, AddressZip and Code.
If the algorithm finds a property with that head, it takes the tail and continues building the tree down from there, splitting the tail up in the way just described.
If the first split does not match, the algorithm moves the split point to the left (Address, ZipCode) and continues.

> 속성 표현식은 앞의 예제와 같이 엔티티와 바로 연결되는 속성들만 참조할 수 있습니다.
> 쿼리 생성시에, 파싱된 속성들이 도메인 클래스가 관리하는 속성인지를 이미 확인했습니다.
> 하지만, 중첩된 속성을 순회하여 제약조건을 정의할 수도 있습니다.
> 다음의 메서드 시그니쳐를 보겠습니다.
> (코드 생략)
> `Person` 객체(사람)가 `Zipcode` 객체(우편번호)가 있는 `Address` 객체(주소)를 가지고 있다 가정하겠습니다.
> 이 경우에, 이 메서드는 `x.address.zipCode` 와 같이 속성을 순회합니다.
> 구문 분석 알고리즘은 처음에 By 절 이하의 전체 부분 (`AddressZipCode`) 을 속성으로 간주하고, 해당 이름을 가진 도메인 클래스가 있는지 검사합니다.
> 만약, 도메인 클래스를 찾으면 해당 클래스를 속성으로 사용합니다.
> 그렇지 않다면, 알고리즘은 카멜 케이스를 기준으로 머리와 꼬리를 크게 나눠서 알맞는 속성을 찾으려고 시도합니다.
> 위 예시에서는, `AddressZip` 과 `Code` 로 쪼갭니다.
> 만약 알고리즘이 머리 부분과 매치되는 속성을 찾으면, 꼬리 부분을 가져와 머리 부분 아래로 트리를 만들고, 꼬리를 위와 같은 방식으로 다시 나눕니다.
> 만약 첫 번째 분할에서 머리 부분과 매치되는 속성을 찾지 못하면, 분할하는 부분을 좀 더 왼쪽으로 옮기고 다시 진행합니다. (`Address`, `ZipCode`)

<br>

Although this should work for most cases, it is possible for the algorithm to select the wrong property.
Suppose the Person class has an addressZip property as well.
The algorithm would match in the first split round already, choose the wrong property, and fail (as the type of addressZip probably has no code property).

To resolve this ambiguity you can use _ inside your method name to manually define traversal points.
So our method name would be as follows:

```java
List<Person> findByAddress_ZipCode(ZipCode zipCode);
```

Because we treat the underscore character as a reserved character, we strongly advise following standard Java naming conventions (that is, not using underscores in property names but using camel case instead).

> 이 방식은 대부분의 경우 잘 동작하지만, 잘못된 속성을 선택하게 되는 경우도 있습니다.
> 만약 `Person` 클래스에 `addressZip` 속성도 있다고 가정해봅시다.
> 알고리즘은 첫 번째 분할에서 매치되는 속성을 찾아, 결국 사용자가 원하지 않는 잘못된 속성을 선택해 실패하게 됩니다. (`addressZip` 이 파라미터로 넘어오지 않았기 때문에)
>
> 이러한 애매모호함을 해결하기 위해, `_` 기호를 메서드 이름에 수동으로 넣어줘 순회 지점을 정의할 수 있습니다.
> 이 경우, 메서드 명은 다음과 같습니다.
> (코드 생략)
>
> 언더스코어 문자를 예약된 문자로 취급하기 때문에, 저희는 Java naming conventions 를 따를 것을 강력히 권장합니다.
> (속성 이름을 언더스코어 대신 카멜 케이스를 사용할 것을 권장)

<br>

### 8.4.4 Paging, Iterating Large Results, Sorting

> 페이징, 대규모 결과 조회, 정렬

<br>

To handle parameters in your query, define method parameters as already seen in the preceding examples.
Besides that, the infrastructure recognizes certain specific types like Pageable and Sort, to apply pagination and sorting to your queries dynamically.
The following example demonstrates these features:

Example 14. Using Pageable, Slice, and Sort in query methods

```java
Page<User> findByLastname(String lastname,Pageable pageable);

        Slice<User> findByLastname(String lastname,Pageable pageable);

        List<User> findByLastname(String lastname,Sort sort);

        List<User> findByLastname(String lastname,Pageable pageable);
```

**Important**
APIs taking Sort and Pageable expect non-null values to be handed into methods.
If you do not want to apply any sorting or pagination, use `Sort.unsorted()` and `Pageable.unpaged()`.


> 쿼리 속 파라미터를 처리하기 위해, 앞의 예제에서 본 것처럼 메서드 파라미터를 정의하였습니다.
> 그 외에도, 인프라는 `Pageable`, `Sort` 와 같은 객체를 사용하여, 페이징과 정렬을 동적으로 수행합니다.
> 다음의 예제들은 이러한 특성들을 보여줍니다.
>
> 예제 14) `Pageable`, `Slice`, `Sort` 를 사용하는 쿼리 메서드
> (코드 생략)
> `Sort` 와 `Pageable` 을 사용하는 API는 널이 아닌값이 전달될 것으로 예상합니다.
> 만약 정렬이나 페이징을 적용하지 않으려면, `Sort.unsorted()` 나 `Pageable.unpaged()` 메서드를 사용하십시오.

<br>

The first method lets you pass an `org.springframework.data.domain.Pageable` instance to the query method to dynamically add paging to your statically defined query.
A Page knows about the total number of elements and pages available.
It does so by the infrastructure triggering a count query to calculate the overall number.
As this might be expensive (depending on the store used), you can instead return a Slice.
A Slice knows only about whether a next Slice is available, which might be sufficient when walking through a larger result set.

> 첫 번째 메서드를 사용하면 `Pageable` 객체를 쿼리 메서드에 전달하여 정적으로 정의된 쿼리에 동적인 페이징을 추가할 수 있습니다.
> `Page` 는 사용 가능한 모든 요소와 페이지 개수를 알고 있습니다.
> 인프라가 카운트 쿼리를 날려 전체적인 숫자를 계산합니다.
> 만약 이것이 고비용의 쿼리인 경우, `Slice` 를 대체제로 사용할 수 있습니다.
> `Slice` 객체는 다음 `Slice` 가 존재하는지만 알고있어, 대용량의 결과물을 조회해야하는 경우, 대용으로 사용할 수 있습니다.

<br>

Sorting options are handled through the Pageable instance, too.
If you need only sorting, add an org.springframework.data.domain.Sort parameter to your method.
As you can see, returning a List is also possible.
In this case, the additional metadata required to build the actual Page instance is not created (which, in turn, means that the additional count query that would have been necessary is not issued).
Rather, it restricts the query to look up only the given range of entities.

**Note**
To find out how many pages you get for an entire query, you have to trigger an additional count query.
By default, this query is derived from the query you actually trigger.

> 정렬 옵션도 `Pageable` 객체를 통해 처리할 수 있습니다.
> 만약 정렬만 필요하다면 `Sort` 객체를 메서드 파라미터로 받으면 됩니다.
> 예제에서 봤듯이, `List` 를 반환하는 것도 가능합니다.
> 이 경우엔, `Page` 객체를 만드는 데 필요한 추가적인 메타 데이터가 생성되지 않습니다. (이는 추가적인 카운트 쿼리가 발생하지 읺음을 의미합니다.)
> 대신, 주어진 범위의 엔티티들만 조회하도록 쿼리를 제한합니다.
>
> **메모**
> 전체 페이지가 몇 개인지 알기 위해선, 카운트 쿼리를 날려야 합니다.
> 기본적으로, 카운트 쿼리는 당신이 작성한 쿼리에서 파생됩니다.

<br>

**Which Method is Appropriate?**
The value provided by the Spring Data abstractions is perhaps best shown by the possible query method return types outlined in the following table below.
The table shows which types you can return from a query method

> **어떤 메서드가 적절한가?**
> Spring Data 추상화가 제공하는 값은 아래 표의 쿼리 메서드의 반환 유형을 통해 알 수 있습니다.
> 아래 표는 쿼리 메서드가 어떤 타입을 반환할 수 있는지 보여줍니다.

<br>
Table 1. Consuming Large Query Results

| Method        | Amount of Data Fetched                                              | 	Query Structure	                     | Constraints                                                                                                                                                                                                                                                                                                                          |
|---------------|---------------------------------------------------------------------|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| List<T>       | All results.                                                        | Single query.                         | Query results can exhaust all memory. Fetching all data can be time-intensive.                                                                                                                                                                                                                                                       | 
| Streamable<T> | All results.                                                        | Single query.                         | Query results can exhaust all memory. Fetching all data can be time-intensive.                                                                                                                                                                                                                                                       | 
| Stream<T>     | Chunked (one-by-one or in batches) depending on Stream consumption. | Single query using typically cursors. | Streams must be closed after usage to avoid resource leaks.                                                                                                                                                                                                                                                                          | 
| Flux<T>       | Chunked (one-by-one or in batches) depending on Flux consumption.   | Single query using typically cursors. | Store module must provide reactive infrastructure.                                                                                                                                                                                                                                                                                   | 
| Slice<T>       | Pageable.getPageSize() + 1 at Pageable.getOffset()                  | One to many queries fetching data starting at Pageable.getOffset() applying limiting.| A Slice can only navigate to the next Slice.   <br/> - Slice provides details whether there is more data to fetch. <br/> - Offset-based queries becomes inefficient when the offset is too large because the database still has to materialize the full result. <br/> - Window provides details whether there is more data to fetch. | 
| Page<T>      | Pageable.getPageSize() at Pageable.getOffset()                      | One to many queries starting at Pageable.getOffset() applying limiting. Additionally, COUNT(…) query to determine the total number of elements can be required.| Often times, COUNT(…) queries are required that are costly. <br/> - Offset-based queries becomes inefficient when the offset is too large because the database still has to materialize the full result.                                                                                                                                                                                                                                                                 | 

> 표 1, 대용량 쿼리 결과물 사용
>
>| 메서드           | 가져온 데이터 양                  | 	쿼리 구조	                                                                           | 제약 조건                                                                                                                                                                                           |
>|---------------|----------------------------|-----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
>| List<T>       | 모든 결과물.                    | 단일 쿼리                                                                             | 쿼리 결과는 모든 메모리를 소진할 수 있습니다. 모든 데이터를 가져오는 데 많은 시간이 소요될 수 있습니다.                                                                                                                                    |
>| Streamable<T> | 모든 결과물.                    | 단일 쿼리                                                                             | 리 결과는 모든 메모리를 소진할 수 있습니다. 모든 데이터를 가져오는 데 많은 시간이 소요될 수 있습니다.                                                                                                                                     |
>| Stream<T>     | 스트림 소비량에 맞는 덩어리.           | 일반적으로 커서를 사용하는 단일 쿼리.                                                             | 리소스 누수를 방지하기 위해, 사용 후에는 스트림을 닫아야 합니다.                                                                                                                                                           |
>| Flux<T>       | 스트림 소비량에 맞는 덩어리.           | 일반적으로 커서를 사용하는 단일 쿼리.                                                             | 스토어 모듈은 반응형 인프라를 제공해야 합니다.                                                                                                                                                                      |
>| Slice<T>      | 현재 오프셋부터 (페이지사이즈 + 1) 개수 만큼. | 오프셋에서 시작하는 데이터를 가져오는 일대다 쿼리에 제한을 적용합니다.                                           | `Slice` 는 다음 `Slice` 로만 이동할 수 있습니다.   <br/> - `Slice` 는 가져올 데이터가 더 있는지 알려줍니다. <br/> - 오프셋 기반 쿼리는 오프셋이 너무 크면 비효율적입니다. 왜냐하면 데이터베이스가 전체 결과를 구체화해야 하기 때문입니다. <br/> - `Window` 는 더 가져올 정보들이 있는지 알려줍니다. |
>| Page<T>       | 현재 오프셋부터 페이지사이즈 개수 만큼. | 오프셋에서 시작하는 데이터를 가져오는 일대다 쿼리에 제한을 적용합니다. 추가적으로, 전체 요소 개수를 파악하기 위한 count 쿼리가 필요합니다. | 종종, 카운트 쿼리는 많은 비용을 필요로 합니다. <br/> - 오프셋 기반 쿼리는 오프셋이 너무 크면 비효율적입니다. 왜냐하면 데이터베이스가 전체 결과를 구체화해야 하기 때문입니다.                                                                                                                                                            |

<br>

**Paging and Sorting**
You can define simple sorting expressions by using property names.
You can concatenate expressions to collect multiple criteria into one expression.

Example 15. Defining sort expressions
```java
Sort sort = Sort.by("firstname").ascending()
  .and(Sort.by("lastname").descending());
```
For a more type-safe way to define sort expressions, start with the type for which to define the sort expression and use method references to define the properties on which to sort.

Example 16. Defining sort expressions by using the type-safe API
```java
TypedSort<Person> person = Sort.sort(Person.class);

Sort sort = person.by(Person::getFirstname).ascending()
  .and(person.by(Person::getLastname).descending());

```

**Note**
TypedSort.by(…) makes use of runtime proxies by (typically) using CGlib, which may interfere with native image compilation when using tools such as Graal VM Native.

If your store implementation supports Querydsl, you can also use the generated metamodel types to define sort expressions:

Example 17. Defining sort expressions by using the Querydsl API
```java
QSort sort = QSort.by(QPerson.firstname.asc())
  .and(QSort.by(QPerson.lastname.desc()));
```

> **페이징과 정렬**
> 속성 이름을 사용해서 간단한 정렬 표현식을 정의할 수 있습니다.
> 표현식을 합쳐 여러 기준을 하나의 표현식으로 정의할 수 있습니다.
>
> 예제 15) 정렬 표현식 정의
> (코드 생략)
> 더욱 type-safe 하게 정렬 표현식을 정의하기 위해서, 정렬 표현식을 정의할 유형부터 시작하고 메서드 참조를 사용하여 정렬할 속성을 정의합니다.
>
> 예제 16) type-safe API 를 사용하여 정렬 표현식 정의하기
> (코드 생략)
>
> **참고**
> `TypedSort.by(...)` 는 CGlib을 사용한 런타임 프록시를 사용하므로, Graal VM Native 툴을 사용할 때 네이티브 이미지 컴파일을 방해할 수 있습니다.
>
> 만약 Querydsl 을 사용한다면, 생성된 메타모델 타입을 사용하여 정렬 표현식을 정의할 수 있습니다.
>
> 예제 17) Querydsl API를 사용해 정렬표현식 정의
> (코드 생략)

<br>

### 8.4.5. Limiting Query Results

> 쿼리 결과 제한

<br>

You can limit the results of query methods by using the first or top keywords, which you can use interchangeably.
You can append an optional numeric value to top or first to specify the maximum result size to be returned.
If the number is left out, a result size of 1 is assumed.
The following example shows how to limit the query size:

Example 18. Limiting the result size of a query with Top and First
```java
User findFirstByOrderByLastnameAsc();

User findTopByOrderByAgeDesc();

Page<User> queryFirst10ByLastname(String lastname, Pageable pageable);

Slice<User> findTop3ByLastname(String lastname, Pageable pageable);

List<User> findFirst10ByLastname(String lastname, Sort sort);

List<User> findTop10ByLastname(String lastname, Pageable pageable);


```

> `first` 나 `top` 키워드를 사용하여, 쿼리 메서드의 결과를 제한할 수 있으며, 이 키워드는 서로 바꿔서 사용할 수 있습니다.
> 당신은 `top` 이나 `first` 뒤에 숫자값을 추가하여 반환할 결과값의 크기를 지정할 수 있습니다.
> 만약 숫자가 없다면 하나만 반환합니다.
> 다음의 예제들은 쿼리 사이즈를 제한하는 방법을 보여줍니다.
>
> 예제 18) `Top`, `First` 키워드를 사용하여 쿼리 결과 크기를 제한하기
> (코드 생략)

<br>

The limiting expressions also support the Distinct keyword for datastores that support distinct queries.
Also, for the queries that limit the result set to one instance, wrapping the result into with the Optional keyword is supported.

If pagination or slicing is applied to a limiting query pagination (and the calculation of the number of available pages), it is applied within the limited result.

**Note**
Limiting the results in combination with dynamic sorting by using a Sort parameter lets you express query methods for the 'K' smallest as well as for the 'K' biggest elements.

> 제한 표현식은 `Distinct` 키워드도 지원합니다.
> 또한 쿼리 결과가 하나인 경우, 결과물은 `Optional` 로 감쌀 수 있습니다.
>
> 만약 페이지네이션과 슬라이싱이 제한된 쿼리에 적용될 경우, (페이지 계산과 같은 동작들은) 제한된 결과 내에서 적용됩니다.
>
> **참고**
> `Sort` 파라미터를 사용하여 객체 크기를 제한하면, `K` 개의 가장 작은 요소뿐만 아니라, `K`개의 가장 큰 요소들을 가져오는 쿼리 메서드를 정의할 수 있습니다.

<br>

### 8.4.6. Repository Methods Returning Collections or Iterables

> `Collections` 또는 `Iterables` 를 반환하는 리포지토리 메서드

<br>

Query methods that return multiple results can use standard Java Iterable, List, and Set.
Beyond that, we support returning Spring Data’s Streamable, a custom extension of Iterable, as well as collection types provided by Vavr.
Refer to the appendix explaining all possible query method return types.

> 쿼리 메서드는 `Iterable`, `List`, `Set` 과 같은 다양한 복수 결과물을 반환할 수 있습니다.
> 게다가, Spring Data의 `Streamable` 이나, Vavr 에서 제공하는 컬렉션 유형들을 반환할 수 있습니다.
> 쿼리 메서드가 반환할 수 있는 모든 반환타입에 대한 참고는 부록을 참고하십시오.

<br>

**Using Streamable as Query Method Return Type**
You can use Streamable as alternative to Iterable or any collection type.
It provides convenience methods to access a non-parallel Stream (missing from Iterable) and the ability to directly ….filter(…) and ….map(…) over the elements and concatenate the Streamable to others:

Example 19. Using Streamable to combine query method results
```java
interface PersonRepository extends Repository<Person, Long> {
  Streamable<Person> findByFirstnameContaining(String firstname);
  Streamable<Person> findByLastnameContaining(String lastname);
}

Streamable<Person> result = repository.findByFirstnameContaining("av")
  .and(repository.findByLastnameContaining("ea"));


```

> **쿼리 메서드의 반환타입으로 `Streamable` 을 사용하기**
> 당신은 `Iterable` 이나 다른 컬렉션 타입을 반환하는 대신, `Streambale` 을 반환할 수 있습니다.
> 이것은 병렬이 아닌 스트림을 사용하는 편리한 메서드들을 제공하고 (`Iterable` 엔 없는), `.filter()` 나 `.map()` 을 사용하여 요소들을 다른 `Streamable` 에 연결할 수 있습니다.
>
> 예제 19) `Streamable` 을 사용하여 쿼리 메서드 결과물을 결합하기
> (코드 생략)

<br>

**Returning Custom Streamable Wrapper Types**

Providing dedicated wrapper types for collections is a commonly used pattern to provide an API for a query result that returns multiple elements.
Usually, these types are used by invoking a repository method returning a collection-like type and creating an instance of the wrapper type manually.
You can avoid that additional step as Spring Data lets you use these wrapper types as query method return types if they meet the following criteria:

1. The type implements Streamable.
2. The type exposes either a constructor or a static factory method named of(…) or valueOf(…) that takes Streamable as an argument.

The following listing shows an example:
```java
class Product {                                         (1)
  MonetaryAmount getPrice() { … }
}

@RequiredArgsConstructor(staticName = "of")
class Products implements Streamable<Product> {         (2)

  private final Streamable<Product> streamable;

  public MonetaryAmount getTotal() {                    (3)
    return streamable.stream()
      .map(Priced::getPrice)
      .reduce(Money.of(0), MonetaryAmount::add);
  }


  @Override
  public Iterator<Product> iterator() {                 (4)
    return streamable.iterator();
  }
}

interface ProductRepository implements Repository<Product, Long> {
  Products findAllByDescriptionContaining(String text);                 (5)
}

```
1. A Product entity that exposes API to access the product’s price.
2. 	A wrapper type for a Streamable<Product> that can be constructed by using Products.of(…) (factory method created with the Lombok annotation).
      A standard constructor taking the Streamable<Product> will do as well.
3. 	The wrapper type exposes an additional API, calculating new values on the Streamable<Product>.
4. 	Implement the Streamable interface and delegate to the actual result.
5. 	That wrapper type Products can be used directly as a query method return type.
      You do not need to return Streamable<Product> and manually wrap it after the query in the repository client.

> **사용자 설정의 `Streamable` 반환 타입**
>
> 컬렉션에 맞는 전용 래퍼 타입을 제공하는 것은 여러 요소를 반환하는 API를 제공할 때 일반적으로 사용되는 패턴입니다.
> 보통, 이러한 타입들은 콜렉션과 유사한 타입을 반환하는 리포지토리 메서드를 호출하고, 래퍼 타입 인스턴스를 수동으로 생성합니다.
> `Spring Data`는 다음의 기준들을 만족하는 경우, 래퍼타입을 쿼리 메서드의 반환타입으로 사용해서 추가적인 과정을 피할 수 있습니다.
>
> 1. 반환 유형은 `Streamable` 을 구현합니다.
> 2. 반환 유형은 `Streamable` 을 인수로 받는 생성자나 `of(...), valueOf(...)` 와 같은 정적 팩토리 메서드에 노출됩니다.
>
> 다음은 예제들입니다.
>
> (코드 생략)
> 1. `Product`는 제품 가격을 알 수 있도록, API를 노출하는 엔티티입니다.
> 2. `Products.of(...)` 을 통해 생성될 수 있는 `Streambale<Product>` 래퍼 타입입니다. (롬복 애노테이션으로 만들어진 팩토리 메서드)
     > `Streamable<Product>` 를 받는 표준 생성자도 가능합니다.
> 3. 래퍼 타입은 `Streamable<Product>` 를 계산하는 추가적인 API 를 제공합니다.
> 4. `Streamable` 인터페이스를 구현하고 실제 결과를 위임합니다.
> 5. Products 래퍼 타입은 쿼리 메서드의 반환타입으로 바로 사용될 수 있습니다.
     > 당신은 `Streamable<Product>` 를 반환하거나, 직접 래핑하여 반환할 필요가 없습니다.

<br>

**Support for Vavr Collections**
Vavr is a library that embraces functional programming concepts in Java.
It ships with a custom set of collection types that you can use as query method return types, as the following table shows:

| Vavr collection type   | Used Vavr implementation type       | Valid Java source types	  |
|------------------------|----------------------------|---------------------------|
| io.vavr.collection.Seq | io.vavr.collection.List                 | java.util.Iterable|
| io.vavr.collection.Set | io.vavr.collection.LinkedHashSet                 | java.util.Iterable|
| io.vavr.collection.Map | io.vavr.collection.LinkedHashMap                 | java.util.Map|

You can use the types in the first column (or subtypes thereof) as query method return types and get the types in the second column used as implementation type, depending on the Java type of the actual query result (third column).
Alternatively, you can declare Traversable (the Vavr Iterable equivalent), and we then derive the implementation class from the actual return value.
That is, a java.util.List is turned into a Vavr List or Seq, a java.util.Set becomes a Vavr LinkedHashSet Set, and so on.

> **`Vavr` 컬렉션 지원**
> `Vavr` 은 자바의 함수형 프로그래밍 개념을 수용하는 라이브러리입니다.
> 이것은 쿼리 메서드 반환타입을 사용할 수 있는 사용자 지정 컬렉션 세트를 제공합니다. (다음 테이블 참조)
> (테이블 생략)
>
> 당신은 첫 번째 열의 타입 (또는 그 하위 타입)을 쿼리 메서드의 반환 타입으로 사용할 수 있고, 실제 가져올 땐, 구현 타입인 두번째 열의 타입으로 가져옵니다.
> 또는, `Traversable` (`Vavr Iterable` 과 동등한) 을 선언한 다음, 실제 반환 값에서 구현 클래스를 파생할 수 있습니다.
> 즉, `java.util.List` 는 `Vavr List` 나 `Seq` 으로 바뀌고, `java.util.Set` 은 `Vavr LinkedHashSet` 으로 바뀝니다.

<br>

### 8.4.7 Streaming Query Results

> 쿼리 결과 streaming

<br>

You can process the results of query methods incrementally by using a Java 8 Stream<T> as the return type.
Instead of wrapping the query results in a Stream, data store-specific methods are used to perform the streaming, as shown in the following example:

Example 20. Stream the result of a query with Java 8 Stream<T>
```java
@Query("select u from User u")
Stream<User> findAllByCustomQueryAndStream();

Stream<User> readAllByFirstnameNotNull();

@Query("select u from User u")
Stream<User> streamAllPaged(Pageable pageable);
```

**Note**
A Stream potentially wraps underlying data store-specific resources and must, therefore, be closed after usage.
You can either manually close the Stream by using the close() method or by using a Java 7 try-with-resources block, as shown in the following example:

Example 21. Working with a Stream<T> result in a try-with-resources block
```java
try (Stream<User> stream = repository.findAllByCustomQueryAndStream()) {
  stream.forEach(…);
}
```

**Note**
Not all Spring Data modules currently support Stream<T> as a return type.

> 당신은 쿼리 메서드의 반환타입으로 자바 8의 `Stream` 을 사용하여 점진적으로 데이터를 처리할 수 있습니다.
> 쿼리 결과물을 `Stream` 으로 감싸는 대신에, 다음 예제와 같이 데이터 저장소의 특화된 메서드를 사용하여 streaming 을 수행합니다.
>
> 예제 20) 자바 8 스트림을 사용하여 쿼리 결과를 streaming 하기
> (코드 생략)
>
> **참고**
> `Stream` 은 기본 데이터 저장소별 리소스를 감싸고 있을 가능성이 있으므로 사용후에 반드시 닫아야 합니다.
> `close()` 메서드를 사용하여 수동으로 `Stream` 을 닫아주거나, 자바 7의 `try-with-resources` 블록을 사용할 수 있습니다.
>
> 예제 21) `try-with-resources` 블록내에서 `Stream` 사용하기
> (코드 생략)
>
> **참고**
> 모든 Spring Data 모듈이 `Stream` 을 지원하지는 않습니다.

<br>

### 8.4.8 Null Handling of Repository Methods

> 리포지토리 메서드의 Null 처리

<br>

As of Spring Data 2.0, repository CRUD methods that return an individual aggregate instance use Java 8’s Optional to indicate the potential absence of a value.
Besides that, Spring Data supports returning the following wrapper types on query methods:

- com.google.common.base.Optional
- scala.Option
- io.vavr.control.Option

Alternatively, query methods can choose not to use a wrapper type at all.
The absence of a query result is then indicated by returning null.
Repository methods returning collections, collection alternatives, wrappers, and streams are guaranteed never to return null but rather the corresponding empty representation.
See “Repository query return types” for details.

> Spring Data 2.0 부터, CRUD 리포지토리 메서드는 단건 조회 시, 자바 8의 `Optional` 객체로 값을 반환하여 해당 값이 없을 가능성을 나타냅니다.
> 그 외에도, Spring Data 는 쿼리 메서드의 반환 타입들로 다음의 래퍼 타입들을 사용할 수 있습니다.
>
> (반환타입 생략)
>
> 또는 쿼리 메서드에서 래퍼 타입을 전혀 사용하지 않도록 선택할 수 있습니다.
> 쿼리 결과가 없는 경우, null 을 반환합니다.
> 컬렉션이나 컬렉션 대체 타입, 래퍼 타입, 스트림을 반환하는 리포지토리 메서드는 null 대신 빈 컬렉션을 반환합니다.
> 자세한 내용은 "리포지토리 쿼리의 반환 타입" 을 참고하십시오.

<br>

Nullability Annotations

> **Nullability 주석**

<br>

You can express nullability constraints for repository methods by using Spring Framework’s nullability annotations.
They provide a tooling-friendly approach and opt-in null checks during runtime, as follows:

- @NonNullApi: Used on the package level to declare that the default behavior for parameters and return values is, respectively, neither to accept nor to produce null values.
- @NonNull: Used on a parameter or return value that must not be null (not needed on a parameter and return value where @NonNullApi applies).
- @Nullable: Used on a parameter or return value that can be null.

Spring annotations are meta-annotated with JSR 305 annotations (a dormant but widely used JSR).
JSR 305 meta-annotations let tooling vendors (such as IDEA, Eclipse, and Kotlin) provide null-safety support in a generic way, without having to hard-code support for Spring annotations.
To enable runtime checking of nullability constraints for query methods, you need to activate non-nullability on the package level by using Spring’s @NonNullApi in package-info.java, as shown in the following example:

Example 22. Declaring Non-nullability in package-info.java
```java
@org.springframework.lang.NonNullApi
package com.acme;
```

> 스프링 프레임워크의 nullability 주석을 사용하여 리포지토리에 nullability 제약 조건을 걸 수 있습니다.
> 다음과 같이 쉬운 접근 방식을 제공하며, 런타임 중에 null 을 체크할 수 있습니다.
>
> - @NonNullApi : 패키지 수준에서 선언하여 사용합니다.
    > 기본 동작은 파라미터와 반환값 모두 null 값을 허용하지 않습니다.
> - @NonNull : null 이 아니어야 하는 파라미터나 반환값에 사용합니다. (@NonNullApi 가 적용된 곳엔 사용하지 않아도 됩니다.)
> - @Nullable : null 이 될 수 있는 파라미터나 반환값에 사용합니다.
>
> 스프링 애노테이션은 JSR 305 애노테이션과 함께 메타 애노테이션으로 사용됩니다. (JSR은 휴면 상태이지만 널리 사용됩니다.)
> JSR 305 메타 애노테이션은 IDEA, Eclipse, Kotlin 과 같은 업체가 스프링 어노테이션을 하드코딩할 필요 없이 일반적인 방식으로 null-safety 지원을 제공합니다
> 쿼리 메서드의 nullability 제약조건을 런타임 시점에 활성화하려면, 다음예제와 같이 스프링의 @NonNullApi 를 사용하여 패키지 수준에서 null이 불가하도록 해야합니다.
>
> 예제 22) package-info.java 의 Non-nullability 선언
> (코드 생략)

<br>

Once non-null defaulting is in place, repository query method invocations get validated at runtime for nullability constraints.
If a query result violates the defined constraint, an exception is thrown.
This happens when the method would return null but is declared as non-nullable (the default with the annotation defined on the package in which the repository resides).
If you want to opt-in to nullable results again, selectively use @Nullable on individual methods.
Using the result wrapper types mentioned at the start of this section continues to work as expected: an empty result is translated into the value that represents absence.

The following example shows a number of the techniques just described:

Example 23. Using different nullability constraints
```java
package com.acme;                                                       (1)

import org.springframework.lang.Nullable;

interface UserRepository extends Repository<User, Long> {

  User getByEmailAddress(EmailAddress emailAddress);                    (2)

  @Nullable
  User findByEmailAddress(@Nullable EmailAddress emailAdress);          (3)

  Optional<User> findOptionalByEmailAddress(EmailAddress emailAddress); (4)
}
```
1. The repository resides in a package (or sub-package) for which we have defined non-null behavior.
2. Throws an EmptyResultDataAccessException when the query does not produce a result.
   Throws an IllegalArgumentException when the emailAddress handed to the method is null.
3. Returns null when the query does not produce a result.
   Also accepts null as the value for emailAddress.
4. Returns Optional.empty() when the query does not produce a result.
   Throws an IllegalArgumentException when the emailAddress handed to the method is null.

> null 이 아닌 기본값이 적용되면, 리포지토리 쿼리 메서드 호출은 런타임에 null 가능성에 대해 검사합니다.
> 만약 쿼리 결과물이 정의된 제약조건을 위배하면, 예외가 발생합니다.
> 이러한 상황은 메서드가 non-nullable 로 정의되어 있는데, null값을 반환할 경우 발생합니다. (리포지토리가 있는 패키지에 정의된 어노테이션의 기본값)
> null 가능성이 있는 결과물을 옵트인하려면, @Nullable 애노테이션을 개별 메서드에서 선택적으로 사용하십시오.
> 이 섹션의 시작부분에서 언급한 result wrapper 타입을 사용하면 예상대로 작동합니다: 빈 결과값은 부재를 나타내는 값으로 변환됩니다.
>
> 다음의 예제는 방금 설명한 몇 가지 기술들을 설명합니다.
>
> 예제 23) 다른 nullability 제약 조건 사용
> (코드 생략)
>
> 1. 리포지토리는 null을 사용할 수 있는 동작을 정의한 패키지에 있습니다.
> 2. 쿼리의 결과물이 없는 경우, `EmptyResultDataAccessException` 을 발생시킵니다.
     > 만약 메서드에 넘겨진 (파라미터) `emailAddress` 가 null 이면 `IllegalArgumentException` 을 발생시킵니다.
> 3. 쿼리의 결과물이 없는 경우, null 을 반환합니다.
     > 또한 `emailAddress` 가 null 일 수 있습니다.
> 4. 쿼리의 결과물이 없는 경우, `Optional.empty()` 를 반환합니다.
     > 만약 메서드에 넘겨진 `emailAddress` 가 null 일 경우 `IllegalArgumentException` 을 발생시킵니다.

<br>

**Nullability in Kotlin-based Repositories**

> **코틀린 베이스 리포지토리에서의 nullability**

<br>

Kotlin has the definition of nullability constraints baked into the language.
Kotlin code compiles to bytecode, which does not express nullability constraints through method signatures but rather through compiled-in metadata.
Make sure to include the kotlin-reflect JAR in your project to enable introspection of Kotlin’s nullability constraints.
Spring Data repositories use the language mechanism to define those constraints to apply the same runtime checks, as follows:

Example 24. Using nullability constraints on Kotlin repositories

```kotlin
interface UserRepository : Repository<User, String> {

  fun findByUsername(username: String): User     (1)

  fun findByFirstname(firstname: String?): User? (2)
}
```
1. The method defines both the parameter and the result as non-nullable (the Kotlin default).
   The Kotlin compiler rejects method invocations that pass null to the method.
   If the query yields an empty result, an EmptyResultDataAccessException is thrown.
2. This method accepts null for the firstname parameter and returns null if the query does not produce a result.

> 코틀린은 언어 자체로 nullability 제약 조건이 정의되어 있습니다.
> 코틀린 코드는 바이트코드로 컴파일되며, 메서드 시그니처로 nullability 제약조건을 표현하지 않고, 컴파일된 메타데이터를 통해 표현합니다.
> kotlin-reflect JAR 를 당신의 프로젝트의 포함시켜 코틀린의 nullability 제약 조건 검사기능을 사용할 수 있습니다.
> 스프링 데이터 리포지토리는 언어 메커니즘을 사용하여 동일한 런타임에 검사하기 위해 다음과 같이 제약 조건을 정의합니다.
>
> 예제 24) Kotlin 리포지토리에서 nullability 제약 조건 사용
> (코드 생략)
> 1. 해당 메서드는 파라미터와 결과 모두 null 이 불가능합니다. (Kotlin 의 기본값)
     > Kotlin 컴파일러는 메서드에 null 을 전달하는 메서드의 호출을 금지합니다.
     > 만약 쿼리 결과가 빈 값인 경우, `EmptyResultDataAccessException` 이 발생합니다.
> 2. 이 메서드는 `firstname` 파라미터가 null 값인 것을 허용하고, 쿼리 결과물이 없는 경우 해당 메서드가 null 을 반환하는 것을 허용합니다.

<br>

### 8.4.9. Asynchronous Query Results

> 비동기 쿼리 결과

<br>

You can run repository queries asynchronously by using Spring’s asynchronous method running capability.
This means the method returns immediately upon invocation while the actual query occurs in a task that has been submitted to a Spring TaskExecutor.
Asynchronous queries differ from reactive queries and should not be mixed.
See the store-specific documentation for more details on reactive support.
The following example shows a number of asynchronous queries:
```java
@Async
Future<User> findByFirstname(String firstname);               (1)

@Async
CompletableFuture<User> findOneByFirstname(String firstname); (2)

```
1. Use java.util.concurrent.Future as the return type.
2. Use a Java 8 java.util.concurrent.CompletableFuture as the return type.


> 스프링의 비동기 메서드 실행 기능을 사용하여 리포지토리 쿼리를 비동기적으로 실행할 수 있습니다.
> 메서드는 호출 즉시 결과물을 반환하지만, 실제 쿼리는 스프링 TaskExecutor에 제출된 작업에 의해 발생한다는 의미입니다.
> 비동기쿼리는 반응형 쿼리와는 다르며 함께 사용될 수 없습니다.
> 반응형 지원에 대 한 내용은 각 저장소의 문서를 참고하십시오.
> 다음의 예제들은 여러 비동기 쿼리들을 보여줍니다.
>
> (코드 생략)
> 1. `java.util.concurrent.Future` 을 반환타입으로 사용
> 2. 자바 8의 `java.util.concurrent.CompletableFuture` 을 반환 타입으로 사용

<br>

## 8.5. Creating Repository Instances

This section covers how to create instances and bean definitions for the defined repository interfaces

> ## 리포지토리 인스턴스 생성하기
>
> 이 섹션에서는 인스턴스와 리포지토리 인터페이스를 정의하기 위한 bean 정의를 생성하는 방법을 다룹니다.

<br>

### 8.5.1. Java Configuration

> 자바 설정파일

Use the store-specific @EnableJpaRepositories annotation on a Java configuration class to define a configuration for repository activation.
For an introduction to Java-based configuration of the Spring container, see JavaConfig in the Spring reference documentation.

A sample configuration to enable Spring Data repositories resembles the following:

Example 25. Sample annotation-based repository configuration
```java
@Configuration
@EnableJpaRepositories("com.acme.repositories")
class ApplicationConfiguration {

  @Bean
  EntityManagerFactory entityManagerFactory() {
    // …
  }
}
```
**Note**
The preceding example uses the JPA-specific annotation, which you would change according to the store module you actually use.
The same applies to the definition of the EntityManagerFactory bean.
See the sections covering the store-specific configuration.

> 자바 구성 클래스에서 `@EnableJpaRepositories` 애노테이션을 사용하여 리포지토리 활성화 구성을 정의합니다.
> 스프링 컨테이너의 자바 기반 구성에 대해서는 스프링 참조문서의 JavaConfig 부분을 참조하십시오.
>
> Spring Data 리포지토리를 활성화하는 샘플 구성은 다음과 유사합니다.
>
> 예제 25) 애노테이션 기반 리포지토리 구성 파일 예시
> (코드 생략)
>
> **참고**
> 앞선 예제는 JPA 에 특화된 애노테이션으로, 당신이 실제로 사용하는 저장소 모듈에 따라 애노테이션은 바뀝니다.
> `EntityManagerFactory` bean 의 정의에도 동일하게 적용됩니다.
> 저장소별 구성에 대한 섹션을 참조하십시오.

<br>

### 8.5.2. Using Filters

> 필터 사용

<br>

By default, the infrastructure picks up every interface that extends the persistence technology-specific Repository sub-interface located under the configured base package and creates a bean instance for it.
However, you might want more fine-grained control over which interfaces have bean instances created for them.
To do so, use filter elements inside the repository declaration.
The semantics are exactly equivalent to the elements in Spring’s component filters.
For details, see the Spring reference documentation for these elements.

For example, to exclude certain interfaces from instantiation as repository beans, you could use the following configuration:

Example 26. Using filters
```java
@Configuration
@EnableJpaRepositories(basePackages = "com.acme.repositories",
    includeFilters = { @Filter(type = FilterType.REGEX, pattern = ".*SomeRepository") },
    excludeFilters = { @Filter(type = FilterType.REGEX, pattern = ".*SomeOtherRepository") })
class ApplicationConfiguration {

  @Bean
  EntityManagerFactory entityManagerFactory() {
    // …
  }
}
```
The preceding example excludes all interfaces ending in SomeRepository from being instantiated and includes those ending with SomeOtherRepository.

> 기본적으로 인프라는 구성된 기본 패키지 아래에 존재하는 persistence 기술에 특화된 리포지토리를 확장하는 모든 하위 인터페이스를 가져와서, 이에 대한 bean 인스턴스를 생성합니다.
> 하지만, 어떤 인터페이스에 대해 bean 인스턴스가 생성되는지 더 세밀하게 제어하고 싶을 수 있습니다.
> 이를 위해, 리포지토리 정의 안에 필터 요소를 넣습니다.
> 의미론적으로 스프링의 컴포넌트 필터에 있는 요소와 정확히 동일합니다.
> 더욱 자세한 내용은, 스프링 공식 문서를 참고하십시오.
>
> 예를 들어, 특정 인터페이스를 리포지토리 bean 의 인스턴스화에서 제외하려면, 다음 설정들을 사용할 수 있습니다.
>
> 예제 26) 필터 사용
> (코드 생략)
>
> 앞선 예제에서는 `SomeRepository` 로 끝나는 모든 인터페이스는 인스턴스화에서 제외되고, `SomeOtherRepository` 로 끝나는 모든 인터페이스는 인스턴스화에 포함됩니다.

<br>

### 8.5.3. Standalone Usage

> 독립적으로 사용

<br>

You can also use the repository infrastructure outside of a Spring container — for example, in CDI environments.
You still need some Spring libraries in your classpath, but, generally, you can set up repositories programmatically as well.
The Spring Data modules that provide repository support ship with a persistence technology-specific RepositoryFactory that you can use, as follows:

Example 27. Standalone usage of the repository factory
```java
RepositoryFactorySupport factory = … // Instantiate factory here
UserRepository repository = factory.getRepository(UserRepository.class);
```

> 리포지토리 인프라를 스프링 컨테이너 밖에서도 사용할 수 있습니다. - 예를 들면, CDI 환경
> 여전히 클래스 경로에 몇 가지의 스프링 라이브러리를 필요로 하지만, 일반적으로 프로그래밍 방식으로도 리포지토리를 설정할 수 있습니다.
> Spring Data 모듈은 persistence 기술에 특화된 `RepositoryFactory` 과 같은 리포지토리 지원을 제공합니다.
>
> 예제 27) `repository factory` 의 독립적인 사용
> (코드 생략)

<br>

## 8.6. Custom Implementations for Spring Data Repositories

> Spring Data 리포지토리의 사용자 정의 구현

<br>

Spring Data provides various options to create query methods with little coding.
But when those options don’t fit your needs you can also provide your own custom implementation for repository methods.
This section describes how to do that.

> Spring Data 리포지토리는 코딩을 거의하지 않고도 쿼리 메서드를 만드는 다양한 옵션이 있습니다.
> 하지만 해당 옵션들이 필요하지 않은 경우, 사용자 정의 리포지토리를 만들 수 있습니다.
> 이 섹션에서 이를 설명합니다.

<br>

### 8.6.1. Customizing Individual Repositories

> 개별 리포지토리 커스터마이징

<br>

To enrich a repository with custom functionality, you must first define a fragment interface and an implementation for the custom functionality, as follows:

Example 28. Interface for custom repository functionality
```java
interface CustomizedUserRepository {
  void someCustomMethod(User user);
}
```

Example 29. Implementation of custom repository functionality
```java
class CustomizedUserRepositoryImpl implements CustomizedUserRepository {

  public void someCustomMethod(User user) {
    // Your custom implementation
  }
}
```
**Note**
The most important part of the class name that corresponds to the fragment interface is the Impl postfix.

> 리포지토리에 사용자의 커스텀 기능을 확장하기 위해서, 먼저 fragment 인터페이스를 정의하고 개별 기능을 구현합니다.
> 다음은 예시입니다.
> 
> 예제 28) 사용자 리포지토리 기능을 위한 인터페이스
> (코드 생략)
>
> 예제 29) 사용자 리포지토리 기능을 위한 구현
> (코드 생략)
> 
> **참고**
> 가장 중요한 부분은 인터페이스를 구현한 클래스 이름은 `인터페이스 이름 + Impl` 이어야 합니다.

<br>

The implementation itself does not depend on Spring Data and can be a regular Spring bean.
Consequently, you can use standard dependency injection behavior to inject references to other beans (such as a JdbcTemplate), take part in aspects, and so on.

Then you can let your repository interface extend the fragment interface, as follows:

Example 30. Changes to your repository interface
```java
interface UserRepository extends CrudRepository<User, Long>, CustomizedUserRepository {

  // Declare query methods here
}
```
Extending the fragment interface with your repository interface combines the CRUD and custom functionality and makes it available to clients.

> 구현 클래스는 Spring Data 에 의존하지 않고, 일반적인 스프링 bean 일 수 있습니다.
> 결과적으로, 당신은 기본적인 의존성 주입을 통해 필요한 다른 bean 들을 주입 받고 (`JdbcTemplate` 같은) 여러가지 작업에 참여할 수 있습니다. 
> 
> 그 다음, 당신의 리포지토리 인터페이스가 fragment 인터페이스를 확장하도록 할 수 있습니다.
> 
> 예제 30) 리포지토리 인터페이스 변경
> (코드 생략)
> 
> 기존의 리포지토리 인터페이스가 fragment 인터페이스를 확장하여 기본적으로 제공하는 CRUD 와 사용자가 만든 기능을 결합하고 사용할 수 있습니다.

<br>

Spring Data repositories are implemented by using fragments that form a repository composition.
Fragments are the base repository, functional aspects (such as QueryDsl), and custom interfaces along with their implementations.
Each time you add an interface to your repository interface, you enhance the composition by adding a fragment.
The base repository and repository aspect implementations are provided by each Spring Data module.

The following example shows custom interfaces and their implementations:

Example 31. Fragments with their implementations
```java
interface HumanRepository {
  void someHumanMethod(User user);
}

class HumanRepositoryImpl implements HumanRepository {

  public void someHumanMethod(User user) {
    // Your custom implementation
  }
}

interface ContactRepository {

  void someContactMethod(User user);

  User anotherContactMethod(User user);
}

class ContactRepositoryImpl implements ContactRepository {

  public void someContactMethod(User user) {
    // Your custom implementation
  }

  public User anotherContactMethod(User user) {
    // Your custom implementation
  }
}
```

The following example shows the interface for a custom repository that extends CrudRepository:

Example 32. Changes to your repository interface
```java
interface UserRepository extends CrudRepository<User, Long>, HumanRepository, ContactRepository {

  // Declare query methods here
}
```

> Spring Data 리포지토리는 리포지토리의 구성물인 fragments 를 사용하여 구현됩니다.
> fragments 는 기본 리포지토리이며, 기능적 측면 및 사용자의 구현이 포함된 사용자 인터페이스입니다. 
> 당신의 리포지토리 인터페이스에 인터페이스를 추가할 때마다, 당신의 리포지토리 구성물에 fragment를 추가하여 확장합니다.
> 기본 리포지토리와 리포지토리 구현은 Spring Data 모듈이 제공합니다.
> 
> 다음의 예제는 사용자 인터페이스와 해당 인터페이스의 구현체를 보여줍니다.
> 
> 예제 31) 구현이 포함된 Fragments
> (코드 생략)
> 
> 다음의 예제는 `CrudRepository` 를 확장한 사용자 리포지토리를 보여줍니다.
> 
> 예제 32) 리포지토리 인터페이스 변경
> (코드 생략)

<br>

Repositories may be composed of multiple custom implementations that are imported in the order of their declaration.
Custom implementations have a higher priority than the base implementation and repository aspects.
This ordering lets you override base repository and aspect methods and resolves ambiguity if two fragments contribute the same method signature.
Repository fragments are not limited to use in a single repository interface.
Multiple repositories may use a fragment interface, letting you reuse customizations across different repositories.

The following example shows a repository fragment and its implementation:

Example 33. Fragments overriding save(…)
```java
interface CustomizedSave<T> {
  <S extends T> S save(S entity);
}

class CustomizedSaveImpl<T> implements CustomizedSave<T> {

  public <S extends T> S save(S entity) {
    // Your custom implementation
  }
}
```

The following example shows a repository that uses the preceding repository fragment:

Example 34. Customized repository interfaces
```java
interface UserRepository extends CrudRepository<User, Long>, CustomizedSave<User> {
}

interface PersonRepository extends CrudRepository<Person, Long>, CustomizedSave<Person> {
}
```

> 리포지토리는 선언된 순서대로 가져오는 여러개의 사용자 정의 구현으로 구성될 수 있습니다. 
> 사용자 정의 구현은 기본 구현보다 더 높은 우선순위를 가집니다.
> 이러한 우선순위는 당신의 기본 리포지토리를 재정의하고, 두 fragments 가 같은 메서드 시그니쳐를 가질때의 애매모호함을 해결합니다.
> 리포지토리 fragments 는 단일 리포지토리 인터페이스에만 사용되도록 제한되지 않습니다.
> 여러개의 리포지토리들이 fragment 인터페이스를 사용할 수 있으므로, 여러개의 다른 리포지토리들은 사용자 정의 기능들을 재사용할 수 있습니다.
> 
> 다음의 예제는 리포지토리 fragment 와 이를 구현한 것을 보여줍니다.
> 
> 예제 33) `save()` 메서드를 재정의하는 Fragments
> (코드 생략)
> 
> 다음의 예제는 리포지토리가 이전에 다른 리포지토리에서 사용된 fragment를 사용하는 것을 보여줍니다.
> 
> 예제 34) 사용자정의 리포지토리 인터페이스
> (코드 생략)

<br>

**Configuration**

> 구성

<br>

The repository infrastructure tries to autodetect custom implementation fragments by scanning for classes below the package in which it found a repository.
These classes need to follow the naming convention of appending a postfix defaulting to Impl.

The following example shows a repository that uses the default postfix and a repository that sets a custom value for the postfix:

Example 35. Configuration example
```java
@EnableJpaRepositories(repositoryImplementationPostfix = "MyPostfix")
class Configuration { … }
```

The first configuration in the preceding example tries to look up a class called com.acme.repository.CustomizedUserRepositoryImpl to act as a custom repository implementation.

> 리포지토리 인프라는 리포지토리를 찾은 패키지 아래에서 클래스를 검색하여 사용자 정의 fragments 를 자동으로 감지합니다.
> 이러한 클래스들은 후위에 `Impl` 을 붙이는 naming 컨벤션을 따라야 합니다 
> 
> 다음의 예제들은 기본 postfix를 사용하거나 사용자 설정의 postfix를 사용하는 리포지토리를 보여줍니다.
> 
> 예제 35) 설정 예시
> (코드 생략)
> 
> 앞서 다룬 첫 번째 설정방식은 사용자 설정 리포지토리 구현체로서 동작하는, `com.acme.repository.CustomizedUserRepositoryImp` 클래스를 찾으려고 시도합니다.

<br>

**Resolution of Ambiguity**

> **모호성 해결**

<br>

If multiple implementations with matching class names are found in different packages, Spring Data uses the bean names to identify which one to use.
Given the following two custom implementations for the CustomizedUserRepository shown earlier, the first implementation is used.
Its bean name is customizedUserRepositoryImpl, which matches that of the fragment interface (CustomizedUserRepository) plus the postfix Impl.

Example 36. Resolution of ambiguous implementations
```java
package com.acme.impl.one;

class CustomizedUserRepositoryImpl implements CustomizedUserRepository {

  // Your custom implementation
}

```
If you annotate the UserRepository interface with @Component("specialCustom"), the bean name plus Impl then matches the one defined for the repository implementation in com.acme.impl.two, and it is used instead of the first one.

> 만약 클래스 이름과 매칭되는 여러개의 구현체가 서로 다른 패키지에 있는 경우, `Spring Data` 는 bean 이름을 사용하여 무엇을 사용할지 식별합니다.
> 앞서 나온 `CustomizedUserRepository` 를 구현한 2개의 사용자 정의 구현체가 주어지면, 첫 번째 구현체가 사용됩니다. 
> bean 이름은 `customizedUserRepositoryImpl` 로, fragment 인터페이스 (`CustomizedUserRepository`) 이름에 `Impl` postfix 를 붙인것과 매칭됩니다.
> 
> 예제 36) 모호한 구현의 해결
> (코드 생략)
> 만약 `UserRepository` 인터페이스에 `@Component("specialCustom")` 애노테이션을 붙였다면, (`com.acme.impl.one`패키지의) 첫 번째 구현체가 아닌 bean 이름에 `Impl` 을 붙인 `com.acme.impl.two` 패키지의 리포지토리 구현체와 매칭됩니다.

<br>

**Manual Wiring**

> 수동 연결

<br>

If your custom implementation uses annotation-based configuration and autowiring only, the preceding approach shown works well, because it is treated as any other Spring bean.
If your implementation fragment bean needs special wiring, you can declare the bean and name it according to the conventions described in the preceding section.
The infrastructure then refers to the manually defined bean definition by name instead of creating one itself.
The following example shows how to manually wire a custom implementation:

Example 37. Manual wiring of custom implementations
```java
class MyClass {
  MyClass(@Qualifier("userRepositoryImpl") UserRepository userRepository) {
    …
  }
}
```

> 만약 당신의 사용자 구현체가 애노테이션 기반으로 구성되어 있고 자동 연결만 사용한다면, 다른 스프링 bean 들과 동일하게 처리되므로 앞선 방식들이 잘 동작합니다.
> 만약 구현체 bean 이 특별한 수동 연결이 필요하면, 당신이 직접 bean 을 이전 섹션에서 다룬 규칙에 맞게 정의하면 됩니다.
> 인프라는 직접 bean 을 만들지 않고, 수동으로 정의된 bean 을 참조합니다.
> 다음의 예제는 사용자 정의 구현체를 수동으로 연결하는 방법을 보여줍니다.
> 
> 예제 37) 사용자 정의 구현체의 수동 연결
> (코드 생략)

<br>

### 8.6.2. Customize the Base Repository

> base 리포지토리 커스터마이징

<br>

The approach described in the preceding section requires customization of each repository interfaces when you want to customize the base repository behavior so that all repositories are affected.
To instead change behavior for all repositories, you can create an implementation that extends the persistence technology-specific repository base class.
This class then acts as a custom base class for the repository proxies, as shown in the following example:

Example 38. Custom repository base class
```java
class MyRepositoryImpl<T, ID>
  extends SimpleJpaRepository<T, ID> {

  private final EntityManager entityManager;

  MyRepositoryImpl(JpaEntityInformation entityInformation,
                          EntityManager entityManager) {
    super(entityInformation, entityManager);

    // Keep the EntityManager around to used from the newly introduced methods.
    this.entityManager = entityManager;
  }

  @Transactional
  public <S extends T> S save(S entity) {
    // implementation goes here
  }
}

```

**Caution**
The class needs to have a constructor of the super class which the store-specific repository factory implementation uses.
If the repository base class has multiple constructors, override the one taking an EntityInformation plus a store specific infrastructure object (such as an EntityManager or a template class).

> 앞의 섹션에서 다룬 접근 방식은 모든 리포지토리가 영향을 받도록 base 리포지토리의 동작을 커스터마이징 하려는 경우, 각각의 리포지토리 인터페이스의 커스터마이징이 필요합니다.
> 모든 리포지토리들의 동작을 바꾸는 대신, 기술 종속적인 base 리포지토리 클래스를 확장하는 구현체를 만들 수 있습니다.
> 이 클래스는 다음 예제와 같이 리포지토리 프록시에 대한 사용자 정의 base 클래스처럼 동작합니다.
> 
> 예제 38) 사용자 정의 리포지토리 base 클래스
> (코드 생략)
> 
> **주의 사항**
> 클래스는 저장소별 리포지토리 팩토리의 구현체를 생성하기위한 super 생성자를 가져야 합니다.
> 만약 리포지토리 base 클래스가 여러개의 생성자를 가진다면, `EntityInformation` 과 스토어 종속적인 객체 (`EntityManager` 나 `template class` 같은) 를 포함하는 생성자를 재정의합니다.

<br>

The final step is to make the Spring Data infrastructure aware of the customized repository base class
In configuration, you can do so by using the repositoryBaseClass, as shown in the following example:

Example 39. Configuring a custom repository base class
```java
@Configuration
@EnableJpaRepositories(repositoryBaseClass = MyRepositoryImpl.class)
class ApplicationConfiguration { … }

```

> 마지막 단계는 Spring Data 인프라가 사용자 정의 base 리포지토리 클래스를 인식하도록 만드는 것입니다.
> 설정 파일에서, 다음의 예제와 같이 `repositoryBaseClass` 를 사용할 수 있습니다. 
> 
> 예제 39) 사용자 정의 base 리포지토리 클래스 설정하기
> (코드 생략)

<br>

## 8.7. Publishing Events from Aggregate Roots

> 총괄적인 루트에서 이벤트 게시

<br>

Entities managed by repositories are aggregate roots.
In a Domain-Driven Design application, these aggregate roots usually publish domain events.
Spring Data provides an annotation called @DomainEvents that you can use on a method of your aggregate root to make that publication as easy as possible, as shown in the following example:

Example 40. Exposing domain events from an aggregate root
```java
class AnAggregateRoot {

    @DomainEvents (1)
    Collection<Object> domainEvents() {
        // … return events you want to get published here
    }

    @AfterDomainEventPublication (2)
    void callbackMethod() {
       // … potentially clean up domain events list
    }
}

```
1. The method that uses @DomainEvents can return either a single event instance or a collection of events.
It must not take any arguments.
2. 	After all events have been published, we have a method annotated with @AfterDomainEventPublication.
You can use it to potentially clean the list of events to be published (among other uses).

The methods are called every time one of the following a Spring Data repository methods are called:
```java
save(…), saveAll(…)

delete(…), deleteAll(…), deleteAllInBatch(…), deleteInBatch(…)
```

Note, that these methods take the aggregate root instances as arguments.
This is why deleteById(…) is notably absent, as the implementations might choose to issue a query deleting the instance and thus we would never have access to the aggregate instance in the first place.

> 리포지토리로 관리되는 엔티티들은 총괄적인 루트입니다.
> 도메인 주도 개발 어플리케이션에서, 이러한 총괄적인 루트들은 도메인 이벤트를 게시합니다.
> Spring Data는 게시를 최대한 쉽게 하기 위해, 총괄적인 루트 메서드에 사용할 수 있는 `@DomainEvents` 애노테이션을 제공합니다.
> 
> 예제 40) 총괄적인 루트에서 도메인 이벤트 노출하기
> (코드 생략)
> 1. `@DomainEvents` 를 사용한 메서드는 하나 혹은 여러개의 이벤트 객체들을 반환할 수 있습니다. 
> 이때 어떠한 매개변수도 없어야 합니다.
> 2. 모든 이벤트들이 게시된 후에, `@AfterDomainEventPublication` 애노테이션을 메서드에 붙일 수 있습니다.
> 당신은 게시할 이벤트 리스트를 잠재적으로 정리할 수 있습니다 (다른 용도 중에서도).
> 
> 이러한 메서드는 다음의 Spring Data 리포지토리의 메서드들이 호출될 때 마다 항상 호출됩니다.
> (코드 생략)
> 
> 참고, 이러한 메서드들은 총괄적인 루트 객체를 인자로서 받습니다.
> 구현이 인스턴스를 삭제하는 쿼리를 실행하도록 선택할 수 있고, 따라서 애초에 총괄적인 객체에 접근할 수 없기 때문에 `deleteById(...)` 가 존재하지 않는 것입니다. 

<br>

## 8.8. Spring Data Extensions

> Spring Data 확장

<br>

This section documents a set of Spring Data extensions that enable Spring Data usage in a variety of contexts.
Currently, most of the integration is targeted towards Spring MVC.

> 현재 섹션에서는 Spring Data 가 다양한 문맥에서 사용될 수 있도록하는 Spring Data 확장들을 설명합니다.
> 현재, 대부분의 통합은 Spring MVC를 대상으로 합니다.

<br>

### 8.8.1. Querydsl Extension

> Querydsl 확장

<br>

Querydsl is a framework that enables the construction of statically typed SQL-like queries through its fluent API.
Several Spring Data modules offer integration with Querydsl through QuerydslPredicateExecutor, as the following example shows:

Example 41. QuerydslPredicateExecutor interface
```java
public interface QuerydslPredicateExecutor<T> {

  Optional<T> findById(Predicate predicate);  (1)

  Iterable<T> findAll(Predicate predicate);   (2)

  long count(Predicate predicate);            (3)

  boolean exists(Predicate predicate);        (4)

  // … more functionality omitted.
}
```
1. Finds and returns a single entity matching the Predicate.
2. Finds and returns all entities matching the Predicate.
3. Returns the number of entities matching the Predicate.
4. Returns whether an entity that matches the Predicate exists.

To use the Querydsl support, extend QuerydslPredicateExecutor on your repository interface, as the following example shows:

xample 42. Querydsl integration on repositories
```java
interface UserRepository extends CrudRepository<User, Long>, QuerydslPredicateExecutor<User> {
}

```
The preceding example lets you write type-safe queries by using Querydsl Predicate instances, as the following example shows:
```java
Predicate predicate = user.firstname.equalsIgnoreCase("dave")
	.and(user.lastname.startsWithIgnoreCase("mathews"));

userRepository.findAll(predicate);

```

> Querydsl 은 프레임워크로, 제공하는 API를 활용해 정적 SQL과 유사한 쿼리를 구성할 수 있도록 합니다.
> 다음 예제에서 볼 수 있듯이, 몇몇의 Spring Data 모듈은 `QuerydslPredicateExecutor` 를 통해 `Querydsl` 과의 통합을 제공합니다.  
> 
> 예제 41) `QuerydslPredicateExecutor` 인터페이스
> (코드 생략)
> 1. `Predicate` 와 일치하는 단일 엔티티를 찾아 반환합니다.
> 2. `Predicate` 와 일치하는 모든 엔티티들을 찾아 반환합니다.
> 3. `Predicate` 와 일치하는 엔티티들의 개수를 반환합니다.
> 4. `Predicate` 와 일치하는 엔티티의 존재 여부를 반환합니다.
> 
> `Querydsl` 의 지원을 사용하려면, 다음 예제와 같이 리포지토리 인터페이스에서 `QuerydslPredicateExecutor` 를 확장하십시오.  
> 
> 예제 42) 리포지토리에서 `Querydsl` 통합
> (코드 생략)
> 앞선 예제대로 하면, 다음 예제와 같이 `Querydsl` 의 `Predicate` 객체를 사용하여 type-safe 쿼리를 작성할 수 있습니다.
> (코드 생략)

<br>

## 8.8.2. Web support

> 웹 지원 

<br>

Spring Data modules that support the repository programming model ship with a variety of web support.
The web related components require Spring MVC JARs to be on the classpath.
Some of them even provide integration with Spring HATEOAS.
In general, the integration support is enabled by using the @EnableSpringDataWebSupport annotation in your JavaConfig configuration class, as the following example shows:

Example 43. Enabling Spring Data web support
```java
@Configuration
@EnableWebMvc
@EnableSpringDataWebSupport
class WebConfiguration {}
```
```xml
<bean class="org.springframework.data.web.config.SpringDataWebConfiguration" />

<!-- If you use Spring HATEOAS, register this one *instead* of the former -->
<bean class="org.springframework.data.web.config.HateoasAwareSpringDataWebConfiguration" />

```
The @EnableSpringDataWebSupport annotation registers a few components. 
We discuss those later in this section. 
It also detects Spring HATEOAS on the classpath and registers integration components (if present) for it as well.

> Spring Data 모듈은 리포지토리 모델과 다양한 웹 지원을 제공합니다.
> 웹 관련 컴포넌트들은 클래스 경로에 Spring MVC JARs 를 요구합니다. 
> 일부는 Spring HATEOAS 와의 통합기능도 제공합니다.
> 일반적으로, 통합 지원은 다음 예제에서 볼 수 있듯이 `@EnableSpringDataWebSupport` 애노테이션을 JavaConfig 설정 클래스에 붙여 활성화할 수 있습니다.
> 
> 예제 43) Spring Data 웹 지원 활성화
> (코드 생략)
> `@EnableSpringDataWebSupport` 애노테이션은 몇가지 컴포넌트들을 등록합니다.
> 이에 대해서는 이 섹션의 뒷부분에서 설명합니다.
> 또한 Spring HATEOAS 를 현재 클래스 경로에서 찾고, 통합 컴포넌트들을 (존재 한다면) 등록합니다.

<br>

**Basic Web Support**

> 기본 웹 지원

<br>

Enabling Spring Data web support in XML
The configuration shown in the previous section registers a few basic components:

- A Using the DomainClassConverter Class to let Spring MVC resolve instances of repository-managed domain classes from request parameters or path variables.
- HandlerMethodArgumentResolver implementations to let Spring MVC resolve Pageable and Sort instances from request parameters.
- Jackson Modules to de-/serialize types like Point and Distance, or store specific ones, depending on the Spring Data Module used.

> XML 을 사용하여 Spring Data 웹 지원을 활성화합니다.
> 이전 섹션에서 보여준 설정파일이 몇가지 기본 컴포넌트들을 등록합니다.
>
> - Spring MVC가 요청 파라미터나 경로 변수를 통해 리포지토리 관리 도메인 클래스의 인스턴스들을 확인하도록 하기 위해, `DomainClassConvert` 클래스를 사용합니다.
> - `HandlerMethodArgumentResolver` 구현을 통해 Spring MVC 가 요청 파라미터를 바탕으로 `Pageable` 이나 `Sort` 인스턴스들을 확인할 수 있도록 합니다 
> - Jackson 모듈을 사용하여 `Point` 나 `Distance` 같은 타입을 역직렬화/직렬화 하거나, 사용하는 Spring Data 모듈에 따라 특정 타입을 저장할 수 있습니다.

<br>

**Using the DomainClassConverter Class**

> `DomainClassConverter` 클래스 사용하기

<br>

The DomainClassConverter class lets you use domain types in your Spring MVC controller method signatures directly so that you need not manually lookup the instances through the repository, as the following example shows:

Example 44. A Spring MVC controller using domain types in method signatures
```java
@Controller
@RequestMapping("/users")
class UserController {

  @RequestMapping("/{id}")
  String showUserForm(@PathVariable("id") User user, Model model) {

    model.addAttribute("user", user);
    return "userForm";
  }
}
```
The method receives a User instance directly, and no further lookup is necessary.
The instance can be resolved by letting Spring MVC convert the path variable into the id type of the domain class first and eventually access the instance through calling findById(…) on the repository instance registered for the domain type.

**Note**
Currently, the repository has to implement CrudRepository to be eligible to be discovered for conversion.

> `DomainClassConverter` 클래스는 도메인 타입을 Spring MVC 컨트롤러 메서드 시그니쳐에서 바로 사용할 수 있게합니다. 
> 그래서 당신은 일일히 리포지토리를 통해 객체를 찾아볼 필요가 없습니다.
> 다음 예제를 참고하십시오.
> 
> 예제 44) 메서드 시그니쳐에서 도메인 타입을 사용하는 Spring MVC 컨트롤러 
> (코드 생략)
> 이 메서드는 추가 조회없이 `User` 객체를 직접 받습니다.
> Spring MVC 가 경로 변수를 도메인 클래스의 id 타입으로 먼저 변환하고, `findById(...)` 메서드를 통해 리포지토리에서 해당 객체에 결국 접근하여 객체를 확인할 수 있습니다.
> 
> **참고**
> 현재, 리포지토리는 `CrudRepository` 를 구현해야 해당 변환을 사용할 수 있습니다.

<br>

**HandlerMethodArgumentResolvers for Pageable and Sort**

> `Pageable` 과 `Sort` 를 위한 `HandlerMethodArgumentResolver` 

<br>

The configuration snippet shown in the previous section also registers a PageableHandlerMethodArgumentResolver as well as an instance of SortHandlerMethodArgumentResolver.
The registration enables Pageable and Sort as valid controller method arguments, as the following example shows:

Example 45. Using Pageable as a controller method argument
```java
@Controller
@RequestMapping("/users")
class UserController {

  private final UserRepository repository;

  UserController(UserRepository repository) {
    this.repository = repository;
  }

  @RequestMapping
  String showUsers(Model model, Pageable pageable) {

    model.addAttribute("users", repository.findAll(pageable));
    return "users";
  }
}

```
The preceding method signature causes Spring MVC try to derive a Pageable instance from the request parameters by using the following default configuration:

Table 2. Request parameters evaluated for Pageable instances
- `page` : Page you want to retrieve. 0-indexed and defaults to 0.
- `size` : Size of the page you want to retrieve. Defaults to 20.
- `sort` : Properties that should be sorted by in the format property,property(,ASC|DESC)(,IgnoreCase). The default sort direction is case-sensitive ascending. Use multiple sort parameters if you want to switch direction or case sensitivity — for example, ?sort=firstname&sort=lastname,asc&sort=city,ignorecase.

> 이전 섹션에서 다뤘던 설정 스니펫은 `PageableHandlerMethodArgumentResolver` 와 `SortHandlerMethodArgumentResolver` 를 등록합니다.
> 등록을 통해 `Pageable` 과 `Sort` 가 다음 예제처럼 컨트롤러의 메서드 파라미터로 사용될 수 있습니다. 
> 
> 예제 45) 컨트롤러 메서드 파라미터로 `Pageable` 사용
> (코드 생략)
> 앞선 메서드 시그니쳐는 Spring MVC 가 다음의 기본 설정을 사용하여 요청 파라미터로부터 `Pageable` 객체를 만들도록 합니다.
> 
> 표 2) `Pageable` 객체를 만들기 위한 요청 파라미터
> - `page` : 확인하고 싶은 페이지입니다. 0부터 시작하며 기본값은 0입니다.
> - `size` : 확인하고 싶은 페이지 하나의 크기입니다. 기본값은 20입니다.
> - `sort` : 정렬해야 하는 속성의 형식은 `속성`, 또는 `속성(,ASC|DESC)(,IgnoreCase)` 형식입니다. 기본 정렬 옵션은 대소문자를 구별하는 오름차순 입니다. 정렬 방향이나 대소문자 구분을 바꾸고 싶다면 여러개의 `sort` 파라미터를 사용하십시오.

<br>

To customize this behavior, register a bean that implements the PageableHandlerMethodArgumentResolverCustomizer interface or the SortHandlerMethodArgumentResolverCustomizer interface, respectively.
Its customize() method gets called, letting you change settings, as the following example shows:

```java
@Bean SortHandlerMethodArgumentResolverCustomizer sortCustomizer() {
    return s -> s.setPropertyDelimiter("<-->");
}
```

If setting the properties of an existing MethodArgumentResolver is not sufficient for your purpose, extend either SpringDataWebConfiguration or the HATEOAS-enabled equivalent, override the pageableResolver() or sortResolver() methods, and import your customized configuration file instead of using the @Enable annotation.
If you need multiple Pageable or Sort instances to be resolved from the request (for multiple tables, for example), you can use Spring’s @Qualifier annotation to distinguish one from another.
The request parameters then have to be prefixed with ${qualifier}_. 
The following example shows the resulting method signature:

```java
String showUsers(Model model,
      @Qualifier("thing1") Pageable first,
      @Qualifier("thing2") Pageable second) { … }
```

You have to populate thing1_page, thing2_page, and so on.

The default Pageable passed into the method is equivalent to a PageRequest.of(0, 20), but you can customize it by using the @PageableDefault annotation on the Pageable parameter.

> 이러한 작업을 커스터마이징 하려면, `PageableHandlerMethodArgumentResolverCustomizer` 인터페이스나 `SortHandlerMethodArgumentResolverCustomizer` 인터페이스를 구현한 빈을 등록하십시오.
> 구현체의 `customize()` 메서드가 호출되면, 다음 예제와 같이 설정을 바꿀 수 있습니다.
> 
> (코드 생략) 
> 
> 만약 속성을 설정할 때, `MethodArgumentResolver` 기능만으로 충분하지 않다면, `SpringDataWebConfiguration` 를 확장하거나 HATEOAS 를 확장하여, `pageableResolver()` 또는 `sortResolver()` 메서드를 오버라이딩하고, `@Enable` 애노테이션 대신 커스터마이징 된 설정 파일을 가져올 수 있습니다.
> 만약 당신이 요청으로부터 여러개의 `Pageable` 또는 `Sort` 객체를 받게된다면, 스프링의 `@Qualifier` 애노테이션을 사용하여 각각을 구별할 수 있습니다.
> 요청 파라미터는 `${qualifier}_` 접두사를 가져야 합니다.
> 다음 예제는 이를 적용한 메서드 시그니처를 보여줍니다.
> 
> (코드 생략)
> 
> 당신은 `thing1_page`, `thing2_page` 등을 채워야 합니다.
> 
> 메서드에 전달되는 기본 `Pageable` 객체는 `PageRequest.of(0, 20)` 와 동일하지만, `Pageable` 파라미터에 `@PageableDefault` 애노테이션을 사용하여 기본 `Pageable` 객체의 설정을 변경할 수 있습니다. 

<br>

**Hypermedia Support for Page and Slice**

> `Page` 와 `Slice` 를 위한 하이퍼미디어 지원

<br>

Spring HATEOAS ships with a representation model class (PagedModel/SlicedModel) that allows enriching the content of a Page or Slice instance with the necessary Page/Slice metadata as well as links to let the clients easily navigate the pages.
The conversion of a Page to a PagedModel is done by an implementation of the Spring HATEOAS RepresentationModelAssembler interface, called the PagedResourcesAssembler.
Similarly Slice instances can be converted to a SlicedModel using a SlicedResourcesAssembler.
The following example shows how to use a PagedResourcesAssembler as a controller method argument, as the SlicedResourcesAssembler works exactly the same:

> Spring HATEOAS 는 표현 모델 클래스 (PageModel / SlicedModel) 를 제공합니다. ->  `Page` 또는 `Slice` 객체를 보강할 수 있는 메타데이터와 페이지를 쉽게 탐색하기 위한 링크들을 포함하는
> `Page` 객체를 `PagedModel` 객체로 변환하는 작업은 `PagedResourcesAssembler` 라 불리는 Spring HATEOAS `RepresentationModelAssembler` 인터페이스 구현체에 의해 수행됩니다.
> 유사하게, `Slice` 객체는 `SlicedResourcesAssembler` 를 사용하여 `SliceModel` 객체로 변환할 수 있습니다.
> 다음 예제는 컨트롤러 메서드의 인자로 `PagedResourcesAssembler` 받아 사용하는 방법을 보여줍니다. 이는 `SlicedResourcesAssembler` 의 동작과정과 똑같습니다.  