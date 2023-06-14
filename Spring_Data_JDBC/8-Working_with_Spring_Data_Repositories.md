# 8. Working with Spring Data Repositories

> 스프링 데이터 리포지토리 사용법  

The goal of the Spring Data repository abstraction is to significantly reduce the amount of boilerplate code required to implement data access layers for various persistence stores.

> Spring Data 리포지토리 추상화의 목표는, 데이터 접근 계층에서 다양한 DB에 접근하기 위해 필요한 상용구 코드들을 상당수 제거하는 것입니다.

<br>

Spring Data repository documentation and your module

This chapter explains the core concepts and interfaces of Spring Data repositories.
The information in this chapter is pulled from the Spring Data Commons module.
It uses the configuration and code samples for the Jakarta Persistence API (JPA) module. 
“Repository query keywords” covers the query method keywords supported by the repository abstraction in general.
 For detailed information on the specific features of your module, see the chapter on that module of this document.

> Spring Data 리포지토리 문서 및 모듈
>
> 이 챕터에서 Spring Data 리포지토리의 핵심 개념과 인터페이스에 대해 설명합니다.
> 이 장의 정보는 Spring Data Commons 모듈에서 가져왔습니다.
> 이 정보들은 JPA 모듈에서 사용되는 구성 및 예제 코드들을 사용합니다.
> "리포지토리 쿼리 키워드" 는 일반적인 리포지토리 추상화에서 지원하는 쿼리 메서드 키워드를 다룹니다.
> 특정 모듈에 대한 더욱 자세한 정보를 원한다면, 특정 모듈에 대해 설명하는 이 문서에 해당하는 장을 찾아 참고하십시오. 

<br>

## 8.1. Core concepts

> 핵심 컨셉

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
>(코드 생략)
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

Note
We also provide persistence technology-specific abstractions, such as `JpaRepository` or `MongoRepository`. 
 Those interfaces extend CrudRepository and expose the capabilities of the underlying persistence technology in addition to the rather generic persistence technology-agnostic interfaces such as CrudRepository.

> 우리는 특정 기술에 맞춰진 추상화를 제공하기도 합니다. (`JpaRepository` 나 `MongoRepository` 같은)
> 이러한 인터페이스들은 `CrudRepository` 를 상속합니다.
> 이러한 인터페이스들은 일반적인 추상화인 `CrudRepository` 기능뿐 아니라, 특정 기술의 기능들을 노출합니다.

<br>

Additional to the CrudRepository, there is a PagingAndSortingRepository abstraction that adds additional methods to ease paginated access to entities:

Example 4. PagingAndSortingRepository interface
```java
public interface PagingAndSortingRepository<T, ID>  {

  Iterable<T> findAll(Sort sort);

  Page<T> findAll(Pageable pageable);
}
```

To access the second page of User by a page size of 20, you could do something like the following:

```java
PagingAndSortingRepository<User, Long> repository = // … get access to a bean
Page<User> users = repository.findAll(PageRequest.of(1, 20));
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
interface PersonRepository extends Repository<Person, Long> { … }
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
class Config { … }
```
Note that the JavaConfig variant does not configure a package explicitly, because the package of the annotated class is used by default. 
To customize the package to scan, use one of the basePackage… attributes of the data-store-specific repository’s @EnableJpaRepositories-annotation.

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
>  (코드 생략)
> 2. 쿼리 메서드를 인터페이스에 선언합니다.
>   (코드 생략)
> 3. JavaConfig 나 XML 설정을 통해 해당 인터페이스에 대한 프록시 인스턴스를 생성할 수 있도록 설정합니다.
>   (코드 생략)
> 애노테이션이 달린 클래스의 패키지가 기본적으로 사용되기 때문에, JavaConfig 변형은 패키지를 명시적으로 설정하지 않습니다.
> 스캔할 패키지를 변경하려면, basePackage 속성 중 하나인 `@EnableJpaRepositories` 애노테이션을 사용하십시오.
> 4. 다음 예제와 같이, 리포지토리를 주입받아 사용하십시오.
>   (코드 생략)
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

If you do not want to extend Spring Data interfaces, you can also annotate your repository interface with @RepositoryDefinition.
Extending one of the CRUD repository interfaces exposes a complete set of methods to manipulate your entities.
If you prefer to be selective about the methods being exposed, copy the methods you want to expose from the CRUD repository into your domain repository.
When doing so, you may change the return type of methods.
Spring Data will honor the return type if possible.
For example, for methods returning multiple entities you may choose Iterable<T>, List<T>, Collection<T> or a VAVR list.

> 정렬을 추상화한 메서드나, 페이징을 추상화한 메서드가 필요한 경우 추가적으로 `PaginAndSortingRepository`, `ReactiveSortingRepository`, `RxJava3SortingRepository`, `CoroutineSortingRepository` 를 확장할 수 있습니다.
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

Note
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
> 이러한 메서드들은 `CrudRepository` 와 메서드 시그니처가 같기 때문에, `Spring Data` 가 제공하는 기본 리포지토리 구현체로 라우팅됩니다. (예를들어, 만약 당신이 JPA를 사용용한다면, `SimpleJpaRepository` 구현체로 라우팅됩니다.) 
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
interface MyRepository extends JpaRepository<User, Long> { }

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends JpaRepository<T, ID> { … }

interface UserRepository extends MyBaseRepository<User, Long> { … }
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
interface AmbiguousRepository extends Repository<User, Long> { … }

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends CrudRepository<T, ID> { … }

interface AmbiguousUserRepository extends MyBaseRepository<User, Long> { … }
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
interface PersonRepository extends Repository<Person, Long> { … }

@Entity
class Person { … }

interface UserRepository extends Repository<User, Long> { … }

@Document
class User { … }
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
interface JpaPersonRepository extends Repository<Person, Long> { … }

interface MongoDBPersonRepository extends Repository<Person, Long> { … }

@Entity
@Document
class Person { … }

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
class Configuration { … }
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

## 8.4. Defining Query Methods

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

> 부록에는 정렬과 대소문자를 포함한 모든 쿼리 메서드 키워드들이 있습니다. 
> 첫 번째 `By` 는 실제 술어 부분을 나누는 구분자 역할을 합니다.
> 기본적인 수준에서, 조건이나 엔티티 속성을 `And` 나 `Or` 로 연결할 수 있습니다.
> 
> 메서드 구문 분석의 실제 결과는 당신이 사용하는 DB에 따라 다릅니다.
> 하지만, 몇 가지 일반적인 주의사항이 있습니다.
> - 일반적으로 표현식은 속성 탐색과 연산자의 조합입니다. 
> 당신은 `AND` 와 `OR` 를 사용해 속성을 결합할 수 있습니다.
> 또한 `Between`, `LessThan`, `GreaterThan`, `Like` 와 같은 연산자를 사용할 수 있습니다.
> 지원되는 연산자는 사용하는 DB에 따라 다르기 때문에, 참조 문서에 해당 부분을 참조하십시오.