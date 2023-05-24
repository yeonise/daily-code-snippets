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
