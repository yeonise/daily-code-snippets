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