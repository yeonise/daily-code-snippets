## Run your tests

### 선행조건(Prerequisites)

Work through the steps to build an image and run it as a containerized application in [Use your
container for development](https://docs.docker.com/language/java/develop/).

> 개발을 위해서 컨테이너 사용에서 이미지를 빌드하고 컨테이너화된 애플리케이션으로 실행하는 단계를 수행합니다.

### 소개(Introduction)

Testing is an essential part of modern software development. Testing can mean a lot of things to
different development teams. There are unit tests, integration tests and end-to-end testing. In this
guide we take a look at running your unit tests in Docker.

> 모던한 소프트웨어 개발의 필수적인 부분은 테스트입니다.
> 테스트는 다양한 개발 팀들에 많은 의미를 부여할 수 있습니다.
> 테스트에는 단위 테스트, 통합 테스트, 종단간 테스트가 있습니다.
> 이 가이드에서는 우리는 도커에서 단위 테스트를 실행하는 것에 대해서 살펴볼 것입니다.

### 테스트들을 실행하기 위해서 Dockerfile 리팩토링하기(Refactor Dockerfile to run tests)

The **Spring Pet Clinic** source code has already tests defined in the test directory
`src/test/java/org/springframework/samples/petclinic`. We can use the following Docker command to
start the container and run tests:

> Spring Pet Clinic 소스 코드는 이미 테스트 디렉토리 `src/test/java/org/springframework/samples/petclinic`
> 에 정의된 테스트들을 가지고 있습니다.
> 우리는 다음과 같은 Docker 명령어를 사용하여 컨테이너를 시작하고 테스트 할 수 있습니다.

```
$ docker run -it --rm --name springboot-test java-docker ./mvnw test
...
[INFO] Results:
[INFO]
[WARNING] Tests run: 40, Failures: 0, Errors: 0, Skipped: 1
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:49 min
```

### 테스트를 위한 멀티-스테이지 Dockerfile(Multi-stage Dockerfile for testing)

Let’s take a look at pulling the testing commands into our Dockerfile. Below is our updated
multi-stage Dockerfile that we will use to build our test image. Replace the contents of your
Dockerfile with the following.

> 이제 테스트 명령을 Dockerfile로 가져오는 방법에 대해서 살펴보겠습니다.
> 다음은 테스트 이미지를 구축하는데 사용할 업데이트된 멀티-스테이지 Dockerfile입니다.
> 다음과 같은 내용을 가진 Dockerfile의 내용을 대체하세요.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy as base
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:resolve
COPY src ./src

FROM base as test
CMD ["./mvnw", "test"]

FROM base as development
CMD ["./mvnw", "spring-boot:run", "-Dspring-boot.run.profiles=mysql", "-Dspring-boot.run.jvmArguments='-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:8000'"]

FROM base as build
RUN ./mvnw package


FROM eclipse-temurin:17-jre-jammy as production
EXPOSE 8080
COPY --from=build /app/target/spring-petclinic-*.jar /spring-petclinic.jar
CMD ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/spring-petclinic.jar"]
```

We added a new build stage labeled `test`. We’ll use this stage for running our tests.

> `test` 레이블이 붙은 새로운 빌드 스테이지를 추가하였습니다.
> 우리는 테스트 수행을 위해서 이 스테이지를 사용할 것입니다.

Now let’s rebuild our image and run our tests. We will run the `docker build` command as above, but
this time we will add the `--target test` flag so that we specifically run the test build stage.

> 지금 우리의 이미지를 재빌드하고 테스트를 실행해봅시다.
> 우리는 위와 같이 `docker build` 명령을 실행합니다. 그러나 이 시점에서 우리는 `--target test` 옵션을 추가할 것입니다.
> 명세적으로 테스트 빌드 스테이지를 수행하기 위해서입니다.

```shell
$ docker build -t java-docker --target test .
[+] Building 0.7s (6/6) FINISHED
...
 => => writing image sha256:967ac80cb7799a5d12a4bdfc67c37b5a6533c6e418c903907d3e86b7d4ebf89a
 => => naming to docker.io/library/java-docker
```

Now that our test image is built, we can run it as a container and see if our tests pass.

> 지금 우리의 테스트 이미지가 빌드되었고, 우리는 컨테이너로서 실행할 수 있고 테스트 통과를 볼 수 있습니다.

```shell
$ docker run -it --rm --name springboot-test java-docker
[INFO] Scanning for projects...
[INFO]
[INFO] ------------< org.springframework.samples:spring-petclinic >------------
[INFO] Building petclinic 2.4.2
...

[INFO] Results:
[INFO]
[WARNING] Tests run: 40, Failures: 0, Errors: 0, Skipped: 1
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:22 min
```

The build output is truncated, but you can see that the Maven test runner was successful and all our
tests passed.

> 빌드 출력이 잘렸습니다. 그러나 여러분들은 메이븐 테스트 러너가 성공적으로 된 것을 볼 수 있고 우리의 모든 테스트가 통과된 것을 볼 수 있습니다.

This is great. However, we’ll have to run two Docker commands to build and run our tests. We can
improve this slightly by using a `RUN` statement instead of the `CMD` statement in the test stage.
The
`CMD` statement is not executed during the building of the image, but is executed when you run the
image in a container. When using the `RUN` statement, our tests run when building the image, and
stop
the build when they fail.

> 정말 멋집니다. 그러나 테스트를 빌드하고 실행하려면 두개의 도커 명령어를 실행해야 합니다.
> 우리는 테스트 스테이지에서 CMD 구문 대신에 RUN 구문를 사용함으로써 개선할 수 있습니다.
> CMD 구문은 이미지의 빌드동안 실행되지 않습니다. 그러나 여러분들이 컨테이너에서 이미지를 실행할때는 실행됩니다.
> RUN 구문을 사용할때 여러분들의 테스트들은 이미지를 빌드할때 실행됩니다. 그리고 테스트가 실패했을때 빌드는 중지됩니다.

Update your Dockerfile with the following.

> 다음과 같이 여러분들의 Dockerfile을 업데이트합니다.

```shell
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy as base
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:resolve
COPY src ./src

FROM base as test
RUN ["./mvnw", "test"]

FROM base as development
CMD ["./mvnw", "spring-boot:run", "-Dspring-boot.run.profiles=mysql", "-Dspring-boot.run.jvmArguments='-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:8000'"]

FROM base as build
RUN ./mvnw package

FROM eclipse-temurin:17-jre-jammy as production
EXPOSE 8080
COPY --from=build /app/target/spring-petclinic-*.jar /spring-petclinic.jar
CMD ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/spring-petclinic.jar"]
```

Now, to run our tests, we just need to run the docker build command as above.

> 테스트를 시랭하기 위해서 우리는 `docker build` 명령어 실행이 필요합니다.

```dockerfile
$ docker build -t java-docker --target test .
[+] Building 27.6s (11/12)
 => CACHED [base 3/6] COPY .mvn/ .mvn
 => CACHED [base 4/6] COPY mvnw pom.xml ./
 => CACHED [base 5/6] RUN ./mvnw dependency:resolve
 => CACHED [base 6/6] COPY src ./src
 => [test 1/1] RUN ["./mvnw", "test"]
 => exporting to image
 => => exporting layers
=> => writing image sha256:10cb585a7f289a04539e95d583ae97bcf8725959a6bd32c2f5632d0e7c1d16a0
=> => naming to docker.io/library/java-docker
```

The build output is truncated for simplicity, but you can see that our tests ran succesfully and
passed. Let’s break one of the tests and observe the output when our tests fail.

> 빌드 출력은 단순함을 위해서 잘랐습니다.
> 그러나 여러분들은 테스트들이 성공적으로 실행되었고 통과되었음을 볼 수 있습니다.
> 테스트중 하나를 중단하고 테스트가 실패했을 때의 출력을 관찰해보겠습니다.

Open the `src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java` file and
change the assertion

> 일단 `src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java` 파일을 열고
> assertion을 변경합니다.

```java
assertThat(violation.getMessage()).isEqualTo("must not be empty");
```

with the following.

> 다음과 같이 변경합니다.

```java
assertThat(violation.getMessage()).isEqualTo("must be empty");
```

Now, run the `docker build` command from above and observe that the build fails and the failing
testing information is printed to the console.

> 지금, 위에서처럼 `docker build` 명령어를 실행하고 빌드 실패와 테스트 실패 정보를 확인해보세요.

```shell
$ docker build -t java-docker --target test .
 => [base 6/6] COPY src ./src
 => ERROR [test 1/1] RUN ["./mvnw", "test"]
...
------
executor failed running [./mvnw test]: exit code: 1
```

### 다음 단계 (Next steps)

In this module, we took a look at running tests as part of our Docker image build process.

In the next module, we’ll take a look at how to set up a CI/CD pipeline using GitHub Actions. See:

> 이번장에서는 우리는 도커 이미지 빌드 처리의 부분으로서 테스트 실행을 살펴봤습니다.
> 다음 장에서는 우리는 GitHub Actions을 사용하여 CI/CD 파이프라인 설정하는 방법에 대해서 살펴보겠습니다.


