## Use containers for development

### 선행조건(Prerequisites)

Work through the steps to build an image and run it as a containerized application
in [Run your image as a container](https://docs.docker.com/language/java/run-containers/).

> [자바 이미지 빌드]에서 java 이미지를 빌드하는 단계를 수행합니다.

### 소개(Introduction)

In this module, we’ll walk through setting up a local development environment for the application we
built in the previous modules. We’ll use Docker to build our images and Docker Compose to make
everything a whole lot easier.

> 이 장에서는 이전 장에서 구축한 애플리케이션에 대한 로컬 개발 환경을 설정하는 과정을 살펴보겠습니다.
> 도커를 사용하여 이미지를 빌드하고 Docker Compose를 사용하여 모든 것을 훨씬 쉽게 만들 것입니다.

### 컨테이너에서 데이버테이스 실행하기(Run a database in a container)

First, we’ll take a look at running a database in a container and how we use volumes and networking
to persist our data and allow our application to talk with the database. Then we’ll pull everything
together into a Compose file which allows us to setup and run a local development environment with
one command. Finally, we’ll take a look at connecting a debugger to our application running inside a
container.

> 첫번째로 우리는 컨테이너에서 데이터베이스를 실행하는 것에 대해서 살펴볼 것입니다.
> 그리고 어떻게 데이터를 보존하고 데이터베이스와 애플리케이션이 통신하는 것을 허용하기 위해서
> 볼륨과 네트워킹을 사용할 것인지 살펴볼 것입니다.
> 그런다음 모든 것을 하나의 명령으로 로컬 개발 환경을 설정하고 실행할 수 있는 Compose 파일로 가져올 것입니다.
> 마지막으로 컨테이너 안에서 실행중인 애플리케이션에 디버거를 연결하는 것에 대해서 살펴볼 것입니다.

Instead of downloading MySQL, installing, configuring, and then running the MySQL database as a
service, we can use the Docker Official Image for MySQL and run it in a container.

> MySQL을 다운로드하고 MySQL 데이터베이스를 서비스로 설치, 구성 및 실행하는 대신 Docker Official Image For MySQL을 사용하여 컨테이너에서 실행할
> 수 있습니다.

Before we run MySQL in a container, we’ll create a couple of volumes that Docker can manage to store
our persistent data and configuration. Let’s use the managed volumes feature that Docker provides
instead of using bind mounts. You can read all
about [Using volumes](https://docs.docker.com/storage/volumes/) in our documentation.

> 컨테이너에서 MySQL을 실행하기 전에 우리는 도커가 지속적인 데이터 및 구성을 저장할 수 있는 몇개의 볼륨을 생성합니다.
> 바인드 마운트를 사용하는 대신에 도커가 제공하는 관리하는 볼륨 기능을 사용합시다.
> 여러분들은 우리들의 문서에서 볼륨을 사용하는 것에 대해서 읽을 수 있습니다.

Let’s create our volumes now. We’ll create one for the data and one for configuration of MySQL.

> 볼륨을 생성해봅시다. 우리는 데이터를 위해서 하나를 생성할 것이고 MySQL의 설정을 위해서 하나를 생성할 것입니다.

```shell
$ docker volume create mysql_data
$ docker volume create mysql_config
```

Now we’ll create a network that our application and database will use to talk to each other. The
network is called a user-defined bridge network and gives us a nice DNS lookup service which we can
use when creating our connection string.

> 지금 우리는 애플리케이션과 데이터베이스가 각각 서로 통신하기 위해서 사용하는 네트워킹을 생성할 것입니다.
> 네트워크는 사용자 정의 브리지 네트워크라고 불리고 우리에게 우리의 연결 문자열을 생성할때 사용할 수 있는 DNS lookup 서비스를 줍니다.

```shell
$ docker network create mysqlnet
```

Now, let’s run MySQL in a container and attach to the volumes and network we created above. Docker
pulls the image from Hub and runs it locally.

> 지금 컨테이너에서 MySQL을 실행해봅시다. 그리고 볼륨을 첨부하고 우리가 위에서 생성한 네트워크도 첨부합니다.
> 도커는 이미지를 허브로부터 내려받고 지역적으로 실행합니다.

```shell
$ docker run -it --rm -d -v mysql_data:/var/lib/mysql \
-v mysql_config:/etc/mysql/conf.d \
--network mysqlnet \
--name mysqlserver \
-e MYSQL_USER=petclinic -e MYSQL_PASSWORD=petclinic \
-e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=petclinic \
-p 3306:3306 mysql:8.0
```

Okay, now that we have a running MySQL, let’s update our Dockerfile to activate the MySQL Spring
profile defined in the application and switch from an in-memory H2 database to the MySQL server we
just created.

> 좋습니다. 지금 우리는 실행중인 MySQL을 가지게 되었습니다.
> 애플리케이션에 정의된 MySQL 스프링 프로파일을 활성화하기 위해서 Dockerfile을 업데이트해봅시다.
> 그리고 in-memory H2 데이터베이스에서 MySQL 서버로 변경해봅시다.

We only need to add the MySQL profile as an argument to the CMD definition.

> 우리는 오직 MySQL 프로파일을 매개변수로 CMD 정의에 추가하기만 하면 됩니다.

```shell
$ vim Dockerfile
Dockerfile 맨 밑에 다음 명령어로 수정
CMD ["./mvnw", "spring-boot:run", "-Dspring-boot.run.profiles=mysql"]
```

Let’s build our image.

> 우리의 이미지를 빌드해봅시다.

```js
$ docker build --tag java-docker .
```

Now, let’s run our container. This time, we need to set the MYSQL_URL environment variable so that
our application knows what connection string to use to access the database. We’ll do this using the
`docker run` command.

> 지금 우리의 컨테이너를 실행해봅시다. 이 시간, 우리는 우리의 애플리케이션이 데이터베이스에 접근하기 위해 연결 정보인 MYSQL_URL 환경 변수 설정이 필요합니다.
> 우리는 `docker run` 명령어를 사용할 것입니다.

```shell
$ docker run --rm -d \
--name springboot-server \
--network mysqlnet \
-e MYSQL_URL=jdbc:mysql://mysqlserver/petclinic \
-p 8080:8080 java-docker
```

Let’s test that our application is connected to the database and is able to list Veterinarians.

> 애플리케이션이 데이터베이스에 연결되었는지 테스트해보고 Veterinarians를 나열할 수 있는지 테스트해봅니다.

```shell
$ curl  --request GET \
  --url http://localhost:8080/vets \
  --header 'content-type: application/json'
```

You should receive the following json back from our service.

> 여러분들은 다음과 같은 json 블록을 서비스로부터 받을 것입니다.

```json
{"vetList":[{"id":1,"firstName":"James","lastName":"Carter","specialties":[],"nrOfSpecialties":0,"new":false},{"id":2,"firstName":"Helen","lastName":"Leary","specialties":[{"id":1,"name":"radiology","new":false}],"nrOfSpecialties":1,"new":false},{"id":3,"firstName":"Linda","lastName":"Douglas","specialties":[{"id":3,"name":"dentistry","new":false},{"id":2,"name":"surgery","new":false}],"nrOfSpecialties":2,"new":false},{"id":4,"firstName":"Rafael","lastName":"Ortega","specialties":[{"id":2,"name":"surgery","new":false}],"nrOfSpecialties":1,"new":false},{"id":5,"firstName":"Henry","lastName":"Stevens","specialties":[{"id":1,"name":"radiology","new":false}],"nrOfSpecialties":1,"new":false},{"id":6,"firstName":"Sharon","lastName":"Jenkins","specialties":[],"nrOfSpecialties":0,"new":false}]}
```

### 개발을 위한 멀티-스테이지(Dockerfile Multi-stage Dockerfile for development)

Let’s take a look at updating our Dockerfile to produce a final image which is ready for production
as well as a dedicated step to produce a development image.

> 이제 Dockerfile을 업데이트하여 프로덕션 준비가 된 최종 이미지와 개발 이미지를 생성하는 전용 단계를 살펴보겠습니다.

We’ll also set up the Dockerfile to start the application in debug mode in the development container
so that we can connect a debugger to the running Java process.

> 우리가 실행중인 자바 프로세스에 대한 디버거를 연결할 수 있도록 개발 컨테이너에 안에 디버거 모드에 대한 애플리케이션을 시작하기 위해서 Dockerfile을 준비할 것입니다.

Below is a multi-stage Dockerfile that we will use to build our production image and our development
image. Replace the contents of your Dockerfile with the following.

> 다음은 멀티 스테이지 우리의 프로덕션 이미지와 개발 이미지 빌드하기 위해서 사용할 Dockerfile입니다.
> 다음을 가지고 여러분들의 Dockerfile의 내용을 대체하세요.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy as base
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:resolve
COPY src ./src

FROM base as development
CMD ["./mvnw", "spring-boot:run", "-Dspring-boot.run.profiles=mysql", "-Dspring-boot.run.jvmArguments='-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:8000'"]

FROM base as build
RUN ./mvnw package

FROM eclipse-temurin:17-jre-jammy as production
EXPOSE 8080
COPY --from=build /app/target/spring-petclinic-*.jar /spring-petclinic.jar
CMD ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/spring-petclinic.jar"]
```

We first add a label to the `FROM eclipse-temurin:17-jdk-jammy` statement. This allows us to refer
to
this build stage in other build stages. Next, we added a new build stage labeled `development`.

> 우리는 첫번째로 `FROM eclipse-temurin:17-jdk-jammy` 명세에 레이블을 추가했습니다.
> 이를 통해 다른 빌드 단계에서 이 빌드 단계를 참조할 수 있습니다.
> 다음으로 우리는 `development`라고 레이블한 새로운 빌드 스테이지를 추가하였습니다.

We expose port 8000 and declare the debug configuration for the JVM so that we can attach a
debugger.

> 우리는 8000 포트를 노출하였고 디버거에 첨부할 수 있도록 JVM에 대한 디버그 설정을 정의하였습니다.

### 지역적으로 개발하기 위한(Compose 사용하기 Use Compose to develop locally)

We can now create a Compose file to start our development container and the MySQL database using a
single command.

> 우리는 개발 컨테이너를 시작하기 위한 컴포즈 파일을 생성할 수 있었고 단일 명령어를 사용하여 MySQL 데이터베이스를 생성할 수 있습니다.

Open the petclinic in your IDE or a text editor and create a new file named docker-compose.dev.yml.
Copy and paste the following commands into the file.

> IDE 또는 텍스트 편집기 안에 petclinic을 열거나 docker-compose.dev.yml 파일을 생성합니다.
> docker-compose.dev.yml 파일로 다음 명령어들을 복사 붙여넣기 합니다.

```yaml
version: '3.8'
services:
  petclinic:
    build:
      context: .
      target: development
    ports:
      - "8000:8000"
      - "8080:8080"
    environment:
      - SERVER_PORT=8080
      - MYSQL_URL=jdbc:mysql://mysqlserver/petclinic
    volumes:
      - ./:/app
    depends_on:
      - mysqlserver

  mysqlserver:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=
      - MYSQL_ALLOW_EMPTY_PASSWORD=true
      - MYSQL_USER=petclinic
      - MYSQL_PASSWORD=petclinic
      - MYSQL_DATABASE=petclinic
    volumes:
      - mysql_data:/var/lib/mysql
      - mysql_config:/etc/mysql/conf.d
volumes:
  mysql_data:
  mysql_config:
```

This Compose file is super convenient as we do not have to type all the parameters to pass to the
docker run command. We can declaratively do that using a Compose file.

> 이 Compoes 파일은 `docker run` 명령어에 전달하기 위해서 모든 매개변수를 입력할 필요가 없기 때문에 매우 편리합니다.

Another really cool feature of using a Compose file is that we have service resolution set up to use
the service names. Therefore, we are now able to use `mysqlserver` in our connection string. The
reason we use `mysqlserver` is because that is what we’ve named our MySQL service as in the Compose
file.

> Compose 파일을 사용하는 또다른 멋진 기능은 서비스 이름을 사용하도록 서비스 해결 방법을 설정했다는 것입니다.
> 그러므로, 우리는 연결 설정에 `mysqlserver`를 사용할 수 있습니다.
> 우리가 `mysqlserver`를 사용하는 이유는 mysqlserver가 우리의 MySQL 서비스를 Compose 파일에 있는 것처럼 사용하기 때문입니다.

Now, to start our application and to confirm that it is running properly.

> 지금, 애플리케이션을 시작하고 제대로 실행하는지 확인하세요.

```shell
$ docker-compose -f docker-compose.dev.yml up --build
```

We pass the `--build` flag so Docker will compile our image and then starts the containers. You
should
see similar output if it runs successfully:

> 도커가 이미지를 컴파일한 다음에 컨테이너를 시작할 수 있도록 `--build` 옵션을 전달합니다.
> 성공적으로 실행된다면 다음과 같은 결과가 출력될 것입니다.

![](https://docs.docker.com/language/java/images/java-compose-output.png)

Now let’s test our API endpoint. Run the following curl commands:

> API 엔드포인트를 테스트하기 위해서 다음 curl 명령어들을 실행합니다.

```shell
$ curl  --request GET \
  --url http://localhost:8080/vets \
  --header 'content-type: application/json'
```

You should receive the following response:

> 여러분들은 다음과 같은 응답을 받을 것입니다.

```shell
{"vetList":[{"id":1,"firstName":"James","lastName":"Carter","specialties":[],"nrOfSpecialties":0,"new":false},{"id":2,"firstName":"Helen","lastName":"Leary","specialties":[{"id":1,"name":"radiology","new":false}],"nrOfSpecialties":1,"new":false},{"id":3,"firstName":"Linda","lastName":"Douglas","specialties":[{"id":3,"name":"dentistry","new":false},{"id":2,"name":"surgery","new":false}],"nrOfSpecialties":2,"new":false},{"id":4,"firstName":"Rafael","lastName":"Ortega","specialties":[{"id":2,"name":"surgery","new":false}],"nrOfSpecialties":1,"new":false},{"id":5,"firstName":"Henry","lastName":"Stevens","specialties":[{"id":1,"name":"radiology","new":false}],"nrOfSpecialties":1,"new":false},{"id":6,"firstName":"Sharon","lastName":"Jenkins","specialties":[],"nrOfSpecialties":0,"new":false}]}
```

### 디버거에 연결하기(Connect a Debugger)

We’ll use the debugger that comes with the IntelliJ IDEA. You can use the community version of this
IDE. Open your project in IntelliJ IDEA and then go to the Run menu > Edit Configuration. Add a new
Remote JVM Debug configuration similar to the following:

> 인텔리제이 IDEA를 가지고 디버거를 할것입니다. 여러분은 IDE의 커뮤니티 버전을 사용할 수 있습니다.
> 인텔리제이 IDEA에서 여러분들의 프로젝트를 여세요. 그리고 menu > Edit Configuration을 여세요.
> 새로운 원격 JVM 디버그 설정을 다음과 비슷하게 추가하세요.

![](https://docs.docker.com/language/java/images/connect-debugger.png)

Let’s set a breakpoint

> 브레이크포인트를 설정해봅시다.

Open the following file `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`
and add a breakpoint inside the `showResourcesVetList` function.

> `src/main/java/org/springframework/samples/petclinic/vet/VetController.java` 파일을 열고
> `showResourcesVetList` 함수안에서 브레이크포인트를 추가하세요.

Start your debug session, **Run** menu and then **Debug NameOfYourConfiguration**

> 디버그 세션을 시작하세요. Run 메뉴 -> Debug 설정이름

![](https://docs.docker.com/language/java/images/debug-menu.png)

You should now see the connection in the logs of your Compose application.

> 여러분들은 지금 Compose 애플리케이션의 로그안에 연결을 볼수 있습니다.

![](https://docs.docker.com/language/java/images/compose-logs.png)

We can now call the server endpoint.

> 우리는 서버 엔드토인트를 호출할 수 있습니다.

```shell
$ curl --request GET --url http://localhost:8080/vets
```

You should have seen the code break on the marked line and now you are able to use the debugger just
like you would normally. You can also inspect and watch variables, set conditional breakpoints, view
stack traces and a do bunch of other stuff.

> 여러분들은 마킹된 라인에서 코드가 브레이크가 된것을 볼 것이고 여러분들이 평소처럼 디버깅 할 수 있습니다.
> 여러분들은 또한 변수들은 검사하고 볼수 있습니다.
> 조건적인 브레이크포인트를 설정하고 스택 추적으로 보고 다른 작업을 수행합니다.

![](https://docs.docker.com/language/java/images/debugger-breakpoint.png)

You can also activate the live reload option provided by SpringBoot Dev Tools. Check out the
SpringBoot documentation for information on how to connect to a remote application.

> 여러분들은 또한 스프링부트 개발 툴에 의해서 제공되는 라이브 재로딩 옵션을 활성화할 수 있습니다.
> 원격 애플리케이션에 연결하는 방법에 대한 정보를 위하여 스프링부트 문서를 확인해보세요.

### 다음 단계(Next steps)

In this module, we took a look at creating a general development image that we can use pretty much
like our normal command line. We also set up our Compose file to expose the debugging port and
configure Spring Boot to live reload our changes.

> 이 장에서는 일반적인 명령어 라인과 비슷하게 사용할 수 있는 일반적인 개발 이미지를 생성하는 방법을 살펴봤습니다.
> 우리는 또한 Compose 파일을 설정하여 디버깅 포트를 노출하고 변경사항을 라이브로 다시 로드하도록 Spring Boot을 구성합니다.

In the next module, we’ll take a look at how to run unit tests in Docker. See

> 다음 장에서는 우리는 도커에서 단위 테스트를 실행하는 방법에 대해서 살펴볼 것입니다.
