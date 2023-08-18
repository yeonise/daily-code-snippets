# 7. Dependencies
> 의존성 

Due to the different inception dates of individual Spring Data modules, most of them carry different major and minor version numbers.
The easiest way to find compatible ones is to rely on the Spring Data Release Train BOM that we ship with the compatible versions defined.
In a Maven project, you would declare this dependency in the <dependencyManagement /> section of your POM as follows:

> 각각의 개별적인 Spring Data 모듈은 다른 개시 날짜를 갖고 있기 때문에, 대부분은 서로 다른 major, minor 버전 번호를 갖고 있습니다. 
> 호환가능한 버전을 찾는 가장 쉬운 방법은 호환 버전과 함께 제공되는 Spring Data Release Train BOM 에 의존하는 것입니다.
> Maven 프로젝트에서, POM의 <dependencyManagement /> 태그 내부에 다음의 의존성들을 선언합니다.

<br>

Example 1. Using the Spring Data release train BOM
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-bom</artifactId>
      <version>2023.0.0</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
  </dependencies>
</dependencyManagement>

```


> Spring Data release train BOM 사용 예
> (xml 생략)

<br>

The current release train version is 2023.0.0.
The train version uses calver with the pattern YYYY.MINOR.MICRO.
The version name follows ${calver} for GA releases and service releases and the following pattern for all other versions:
${calver}-${modifier}, where modifier can be one of the following:

> 현재 배포된 train 버전은 2023.0.0 입니다.
> train 버전은 YYYY.MINOR.MICRO 형태의 날짜 버저닝 패턴을 사용합니다. 
> GA 릴리즈와 서비스 릴리즈의 버전 이름은 calver 형태를 따르고, 다른 모든 버전들은 다음 패턴을 따릅니다. 
> `${calver}-${modifier}` 형태 , 여기서 modifier 는 다음 중 하나일 수 있습니다.
> - SNAPSHOT : 현재 스냅샷
> - M1, M2 and so on: 마일스톤 
> - RC1, RC2, and so on: 릴리즈 후보

<br>

You can find a working example of using the BOMs in our Spring Data examples repository.
With that in place, you can declare the Spring Data modules you would like to use without a version in the <dependencies /> block, as follows:

> 당신은 BOM을 사용한 예제를 우리의 Spring Data 예제 리포지토리에서 찾을 수 있습니다.
> <dependencies /> 블록에 버전 입력 없이 Spring Data 모듈의 의존성을 추가하려는 경우, 다음과 같이 할 수 있습니다.

<br>

Example 2. Declaring a dependency to a Spring Data module
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
  </dependency>
<dependencies>
```

> Spring Data 모듈에 대한 의존성 선언
> (xml 생략)

<br>

# 7.1. Dependency Management with Spring Boot

> 스프링부트의 의존성 관리

Spring Boot selects a recent version of the Spring Data modules for you.
If you still want to upgrade to a newer version, set the spring-data-bom.version property to the train version and iteration you would like to use.

See Spring Boot’s documentation (search for "Spring Data Bom") for more details.

> 스프링 부트는 가장 최신 버전의 Spring Data 모듈을 선택합니다.
> 만약 최신 버전으로 업그레이드 하고싶다면 spring-data-bom.version 속성을 사용하고 싶은 train version 과 iteration 으로 설정하세요.
>
> 더욱 자세한 정보는 스프링 부트의 문서를 확인하세요.

<br>

# 7.2. Spring Framework

> 스프링 프레임워크

The current version of Spring Data modules require Spring Framework 6.0.9 or better.
The modules might also work with an older bugfix version of that minor version.
However, using the most recent version within that generation is highly recommended.

> 현재 사용하는 Spring Data 버전을 사용하기 위해, 스프링 프레임워크는 6.0.9 버전 이상이 필요합니다.
> 모듈은 해당 마이너 버전의 이전 버그 수정 버전에서도 작동할 수 있습니다.
> 하지만, 해당 세대내에서 가장 최신 버전을 사용하는 것을 추천합니다. 
