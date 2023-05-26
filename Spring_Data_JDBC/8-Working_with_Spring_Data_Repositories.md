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
