## Build your Java image

### 전제 조건(Prerequisites)

- You understand basic [Docker concepts](https://docs.docker.com/get-started/overview/).
- You’re familiar with
  the [Dockerfile format](https://docs.docker.com/build/building/packaging/#dockerfile).
- You have enabled [BuildKit](https://docs.docker.com/build/buildkit/#getting-started) on your
  machine.

> - 기본적인 도커 개념
> - Dockerfile 형식
> - 머신에 빌드킷을 사용하도록 설정

### 개요(Overview)

Now that we have a good overview of containers and the Docker platform, let’s take a look at
building our first image. An image includes everything needed to run an application - the code or
binary, runtime, dependencies, and any other file system objects required.

> 이제 우리는 컨테이너와 도커 플랫폼에 대한 개요를 알게 되었습니다.
> 우리의 첫번째 이미지 빌드하는 것을 봅시다.
> 이미지는 애플리케이션을 실행하기 위해 필요한 모든 것을 포함하고 있습니다.
> 코드, 이진, 런타임, 의존성, 다른 파일 시스템들이 요구됩니다.

To complete this tutorial, you need the following:

> 이 튜토리얼을 완성하기 위해서는 다음과 같은 것들이 필요합니다.

- Docker running locally. Follow the instructions
  to [download and install Docker](https://docs.docker.com/get-docker/)
- A Git client
- An IDE or a text editor to edit files. We recommend
  using [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/?section=windows.)

> - 도커를 로컬에서 실행하기. 도커를 다운로드하고 설치하기 위해서 다음 명령어들을 따라하세요.
> - 깃 클라이언트
> - 파일을 편집하기 위해서 텍스트 에디터나 IDE. 인텔리제이를 추천합니다.

### 샘플 애플리케이션(Sample application)

Let’s clone the sample application that we’ll be using in this module to our local development
machine. Run the following commands in a terminal to clone the repo.

> 이 모듈에서 사용할 샘플 애플리케이션을 로컬 개발 머신에 복사해 보겠습니다.
> 저장소를 복사하기 위해서 터미널에서 다음 명령어들을 실행합니다.

```shell
$ cd /path/to/working/directory
$ git clone https://github.com/spring-projects/spring-petclinic.git
$ cd spring-petclinic
```

### 도커 없이 애플리케이션 테스트(선택적) (Test the application without Docker (optional))

In this step, we will test the application locally without Docker, before we continue with building
and running the application with Docker. This section requires you to have Java OpenJDK version 15
or later installed on your machine. [Download and install Java](https://jdk.java.net/)

> 도커를 가지고 애플리케이션을 빌드하고 실행하는 것을 가지기 전에
> 이번 단계에서는 도커 없이 애플리케이션을 지역적으로 테스트 할것입니다.
> 이번 장에서는 JDK 15 이후의 버전이 머신에 설치되어 있어야 합니다.
> 자바를 다운로드하고 설치하세요.

If you prefer to not install Java on your machine, you can skip this step, and continue straight to
the next section, in which we explain how to build and run the application in Docker, which does not
require you to have Java installed on your machine.

> 만약 머신에 자바를 설치하는 것을 원하지 않는다면 이 단계를 스킵해도 됩니다. 그리고 바로 다음 장으로 진행해도 됩니다.
> 우리는 다음장에서 도커에 애플리케이션을 빌드하고 실행하는 방법에 대해서 설명합니다.
> 여러분들의 머신에 자바를 설치하는 것을 요구하지는 않습니다.

Let’s start our application and make sure it is running properly. Maven will manage all the project
processes (compiling, tests, packaging, etc). The Spring Pets Clinic project we cloned earlier
contains an embedded version of Maven. Therefore, we don’t need to install Maven separately on your
local machine.

> 애플리케이션을 시작하고 제대로 실행되는지 확인해봅시다.
> 메이븐은 모든 프로젝트 실행을 관리할 것입니다.(컴파일, 테스트, 패키징 등)
> 이 Spring Pets Clinic 프로젝트는 앞서 복사한 프로젝트입니다. 이 프로젝트는 메이븐의 내장 버전이 포함되어 있습니다.
> 그러므로 우리는 머신에 메이븐을 별도로 설치하지 않아도 됩니다.

Open your terminal and navigate to the working directory we created and run the following command:

> 터미널을 열고 우리가 만든 작업용 디렉토리로 이동하세요. 그리고 다음 명령어를 실행하세요.

```shell
$ ./mvnw spring-boot:run
```

This downloads the dependencies, builds the project, and starts it.

> 이것은 의존성 다운로드들입니다. 프로젝트를 빌드하고 실행하세요.

To test that the application is working properly, open a new browser and navigate
to `http://localhost:8080`.

> 애플리케이션 제대로 동작하는지 테스트하기 위해서 새로운 브라우저를 열고 `http://localhost:8080`에 접속하세요.

Switch back to the terminal where our server is running and you should see the following requests in
the server logs. The data will be different on your machine.

> 서버가 실행중인 터미널로 다시 전환되ㅕㄴ 서버 로그에 다음과 같은 요청들이 표시됩니다.
> 이 데이터는 머신마다 다릅니다.

```shell
o.s.s.petclinic.PetClinicApplication     : Started
PetClinicApplication in 11.743 seconds (JVM running for 12.364)
```

Great! We verified that the application works. At this stage, you’ve completed testing the server
script locally.

> 좋습니다. 우리는 애플리케이션 동작을 확인하였습니다.
> 여러분들은 로컬 머신에서 서버 스크립트 테스트하는 것을 완료했습니다.

Press `CTRL-c` from within the terminal session where the server is running to stop it.

> CTRL-c를 눌러서 서버를 중지합니다.

We will now continue to build and run the application in Docker.

> 우리는 도커에서 이 애플리케이션을 빌드하고 실행할 것입니다.

### 자바에 대한 Dockerfile 생성하기(Create a Dockerfile for Java)

Next, we need to add a line in our Dockerfile that tells Docker what base image we would like to use
for our application.

> 다음으로 Dockerfile에 애플리케이션에 사용할 기본 이미지를 Docker에게 알려주는 명령어들이 필요합니다.

```Dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy
```

Docker images can be inherited from other images. For this guide, we use Eclipse Termurin, one of
the most popular official images with a build-worthy JDK.

> 도커 이미지들은 다른 이미지로부터 상속받는 것이 가능합니다.
> 이 가이드에서는 우리는 Eclipse Termurin을 사용합니다.
> 빌드 가능한 JDK로 가장 인기있는 공식적인 이미지 중 하나입니다.

To make things easier when running the rest of our commands, let’s set the image’s working
directory. This instructs Docker to use this path as the default location for all subsequent
commands. By doing this, we do not have to type out full file paths but can use relative paths based
on the working directory.

> 나머지 명령어들을 쉽게 실행할 수 있도록 이미지의 작업 디렉토리를 설젖ㅇ합니다.
> 도커가 이 경로를 모든 후속 명령의 기본 위치로 사용하도록 지시합니다.
> 이렇게 함으로써 전체 파일 경로를 입력할 필요는 없지만 작업 디렉토리를 기준으로 상대 경로를 사용할 수 있습니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
```

Usually, the very first thing you do once you’ve downloaded a project written in Java which is using
Maven for project management is to install dependencies.

> 보통 프로젝트 관리를 위해 메이븐을 사용하는 자바 프로젝트를 다운로드 받은 후에 가장 먼저 수행되는 작업은 종속성을 설치하는 것입니다.

Before we can run `mvnw dependency`, we need to get the Maven wrapper and our `pom.xml` file into
our
image. We’ll use the `COPY` command to do this. The `COPY` command takes two parameters. The first
parameter tells Docker what file(s) you would like to copy into the image. The second parameter
tells Docker where you want that file(s) to be copied to. We’ll copy all those files and directories
into our working directory - `/app`.

> `mvnw 종속성`을 실행하기 전에 우리는 메이븐 래퍼와 pom.xml 파일을 이미지로 가져오는 것이 필요합니다.
> 이미지로 가져오기 위해서 COPY 명령어를 사용할 것입니다.
> COPY 명령어는 두개의 파라미터를 가집니다. 첫번째 파라미터는 이미지에 복사할 파일을 도커에게 알려줍니다.
> 두번째 매개변수는 해당 파일을 복사할 위치를 도커에게 알려줍니다.
> 우리는 모든 이 파일들을 작업 디렉토리인 /app 디렉토리로 복사할 것입니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
```

Once we have our `pom.xml` file inside the image, we can use the `RUN` command to execute the
command
`mvnw dependency:resolve`. This works exactly the same way as if we were running `mvnw` (or `mvn`)
dependency locally on our machine, but this time the dependencies will be installed into the image.

> 일단 pom.xml 파일이 이미지 안으로 들어가면, `mvnw dependency:resolve` 명령어를 실행하기 위해서 `RUN` 명령어를 사용할 수 있습니다.
> 이 작업은 정확히 여러분들의 머신에 지역적으로 mvnw dependency를 시랭하는 것과 동일합니다.
> 그러나 이번에는 종속성이 이미지에 설치됩니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw depedency:resolve
```

At this point, we have an Eclipse Termurin image that is based on OpenJDK version 17, and we have
also installed our dependencies. The next thing we need to do is to add our source code into the
image. We’ll use the COPY command just like we did with our pom.xml file above.

> 이 시점에서 우리는 OpenJDK 17 기반 Eclipse Termurin 이미지를 가지고 있습니다.
> 그리고 우리는 설치된 종속성들도 가지고 있습니다.
> 다음 생각할 것은 우리는 우리의 소스 코드를 이미지로 추가하는 것이 필요합니다.
> 위의 pom.xml 파일과 마찬가지로 COPY 명령어를 사용합니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw depedency:resolve
COPY src ./src
```

This COPY command takes all the files located in the current directory and copies them into the
image. Now, all we have to do is to tell Docker what command we want to run when our image is
executed inside a container. We do this using the CMD command.

> COPY 명령어는 현재 디렉토리에 있는 모든 파일을들을 복사할 것입니다. 그리고 이미지에 붙여넣을 것입니다.
> 이제 이미지가 컨테이너 안에서 실행될때 실행할 명령어를 도커에게 알려주기만 하면 됩니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw depedency:resolve
COPY src ./src
CMD ["./mvnw", "spring-boot:run"]
```

Here’s the complete Dockerfile.

> 완성된 Dockerfile은 다음과 같습니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy

WORKDIR /app

COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:resolve

COPY src ./src

CMD ["./mvnw", "spring-boot:run"]
```

### .dockerignore 파일 생성하기(Create a .dockerignore file)

To increase the performance of the build, and as a general best practice, we recommend that you
create a .dockerignore file in the same directory as the Dockerfile. For this tutorial, your
.dockerignore file should contain just one line:

> 빌드 성능을 높이기 위해서 일반적인 모범 사례로서 우리는 .dockerignore 파일을 Dockerfile과 같은 위치에 생성하기를 권장합니다.
> 이 튜토리얼의 경우 .dockerignore 파일에는 한줄만 포함되어야 합니다.

```.ignore
target
```

This line excludes the `target` directory, which contains output from Maven, from the Docker `build
context`. There are many good reasons to carefully structure a `.dockerignore` file, but this
one-line
file is good enough for now.

> 이 라인은 Docker의 `build context`에서 메이븐의 출력을 포함하는 `target` 디렉토리를 제외합니다.
> .dockerignore 파일을 신중하게 구성해야 하는 많은 이유가 있지만 현재로서는 이 한줄짜리 파일로 충분합니다.

### 이미지 빌드하기(Build an image)

Now that we’ve created our Dockerfile, let’s build our image. To do this, we use the `docker build`
command. The `docker build` command builds Docker images from a Dockerfile and a “context”. A
build’s
context is the set of files located in the specified PATH or URL. The Docker build process can
access any of the files located in this context.

> 지금 우리는 Dockerfile을 생성하였습니다. 이미지를 빌드해봅시다. 그러기 위해서는 `docker build` 명령어를 사용해야 합니다.
> `docker build` 명령어는 Dockerfile과 컨텍스트로부터 도커 이미지를 빌드하는 명령어입니다.
> 빌드의 컨텍스트란 명세된 PATH 또는 URL에 위치한 파일 집합입니다.
> 도커 빌드 처리는 이 컨텍스트에 위치한 어떤 파일이든 접근할 수 있습니다.

The build command optionally takes a `--tag` flag. The tag is used to set the name of the image and
an
optional tag in the format `name:tag`. We’ll leave off the optional `tag` for now to help simplify
things. If we do not pass a tag, Docker uses “latest” as its default tag. You can see this in the
last line of the build output.

> 빌드 명령어는 선택적으로 `--tag` 옵션을 붙일 수 있습니다. tag는 이미지의 이름을 설정하는데 사용됩니다.
> 그리고 `name:tag` 형식으로 선택적으로 붙일 수 있습니다.
> 우리는 단순하게 돕기 위해서 태그는 일단 생략하겠습니다.
> 만약 우리가 tag를 전달할 수 없다면 도커는 기본 태그로서 "latest" 태그를 사용합니다.
> 여러분들은 빌드 결과의 마지막 줄에서 "latest" 태그를 볼 수 있습니다.

Let’s build our first Docker image.

> 우리의 첫번째 도커 이미지를 빌드해봅시다.

```shell
$ docker build --tag java-docker .
```

```shell
Sending build context to Docker daemon  5.632kB
Step 1/7 : FROM eclipse-temurin:17-jdk-jammy
Step 2/7 : WORKDIR /app
...
Successfully built a0bb458aabd0
Successfully tagged java-docker:latest
```

### 로컬 이미지 보기(View local images)

To see a list of images we have on our local machine, we have two options. One is to use the CLI and
the other is to use [Docker Desktop](https://docs.docker.com/desktop/use-desktop/images/). As we are
currently working in the terminal let’s take a look at
listing images using the CLI.

> 로컬 머신에서 우리가 가진 이미지의 리스트를 보기 위해서 두가지 옵션이 있습니다.
> 하나는 CLI을 사용하는 것이고 다른 하나는 도커 데스크톱을 사용하는 것입니다.
> 우리는 현재 터미널에서 작업하고 있기 때문에 CLI을 사용하여 이미지들의 목록을 봅시다.

To list images, simply run the docker images command.

> 이미지들의 목록을 보기 위하여 간단하게 `docker images` 명령어를 실행하세요.

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED          SIZE
java-docker         latest              b1b5f29f74f0        47 minutes ago   567MB
```

You should see at least the image we just built java-docker:latest.

> 여러분들은 java-docker:latest 이미지를 볼 수 있을 것입니다.

### 태그 이미지(Tag images)

An image name is made up of slash-separated name components. Name components may contain lowercase
letters, digits, and separators. A separator is defined as a period, one or two underscores, or one
or more dashes. A name component may not start or end with a separator.

> 이미지 이름은 슬래시로 구분된 이름 구성요소로 구성됩니다.
> 이름 구성요소들은 소문자, 숫자, 구분자가 포함될 수 있습니다.
> 구분자는 마침표, 하나 또는 두개의 밑줄 또는 하나 이상의 대시로 정의됩니다.
> 이름 구성요소는 구분자를 가지고 시작하거나 종료되지 않을 수 있습니다.

An image is made up of a manifest and a list of layers. Do not worry too much about manifests and
layers at this point other than a “tag” points to a combination of these artifacts. You can have
multiple tags for an image. Let’s create a second tag for the image we built and take a look at its
layers.

> 이미지는 manifest와 레이어들의 목록으로 구성됩니다.
> 태그가 이러한 아티팩트들의 조합을 가리키는 것 이외에는 이 시점에서 manifest와 레이어에 대해서 너무 걱정하지 마세요.
> 여러분들은 이미지에 대한 여러개의 태그들을 가질 수 있습니다.
> 우리가 빌드한 이미지를 대상으로 두번째 태그를 생성해봅시다. 그리고 레이어를 확인해봅시다.

To create a new tag for the image we’ve built above, run the following command:

> 우리가 위에서 빌드한 이미지에 새로운 태그를 생성하기 위해서는 다음 명령어를 실행하세요.

```shell
$ docker tag java-docker:latest java-docker:v1.0.0
```

The `docker tag` command creates a new tag for an image. It does not create a new image. The tag
points to the same image and is just another way to reference the image.

> `docker tag` 명령어는 이미지를 대상으로 새로운 태그를 생성합니다.
> 새로운 태그를 생성하는 것은 새로운 이미지를 생성하지 않습니다.
> 태그는 같은 이미지를 대상으로 가리킵니다. 그리고 이미지를 참조하는 또다른 방법입니다.

Now, run the `docker images` command to see a list of our local images.

> 지금 로컬 이미지들의 목록을 보기 위해서 `docker images` 명령어를 실행하세요.

```shell
$ docker images
REPOSITORY    TAG      IMAGE ID		  CREATED		  SIZE
java-docker   latest   b1b5f29f74f0	  59 minutes ago	567MB
java-docker   v1.0.0   b1b5f29f74f0	  59 minutes ago	567MB
```

You can see that we have two images that start with java-docker. We know they are the same image
because if you take a look at the IMAGE ID column, you can see that the values are the same for the
two images.

> java-docker를 가지고 시작하는 우리가 가진 두개의 이미지들을 볼 수 있습니다.
> 우리는 두개의 이미지들이 같은 이미지인 것을 알 수 있습니다. 왜냐하면 IMAGE ID 컬럼 값이 동일하기 때문입니다.

Let’s remove the tag that we just created. To do this, we’ll use the `rmi` command. The `rmi`
command
stands for “remove image”.

> 우리가 방금 생성한 태그를 삭제해봅시다. 그러기 위해서는 우리는 `rmi` 명령어를 사용할 것입니다.
> `rmi` 명령어는 "remove image"의 줄임말입니다.

```shell
$ docker rmi java-docker:v1.0.0
Untagged: java-docker:v1.0.0
```

Note that the response from Docker tells us that the image has not been removed but only “untagged”.
You can check this by running the `docker images` command.

> 도커로부터의 응답은 우리에게 이미지가 삭제되지 않고 오직 "untagged"라고 응답하고 있습니다.
> 여러분들은 `docker images` 명령어를 실행해서 이것을 확인할 수 있습니다.

```shell
$ docker images
REPOSITORY      TAG     IMAGE ID        CREATED              SIZE
java-docker    	latest	b1b5f29f74f0	59 minutes ago	     567MB
```

Our image that was tagged with `:v1.0.0` has been removed, but we still have the java-docker:latest
tag available on our machine.

> `:v1.0.0` 태그가 삭제되었습니다. 그러나 우리는 여전히 우리의 머신에서 이용가능한 java-docker:latest 태그를 가지고 있습니다.

### 다음 단계(Next steps)

In this module, we took a look at setting up our example Java application that we’ll use for the
rest of the tutorial. We also created a Dockerfile that we used to build our Docker image. Then, we
took a look at tagging our images and removing images. In the next module, we’ll take a look at how
to:

> 이 모듈에서는 튜토리얼의 나머지 부분에 사용할 자바 애플리케이션 예제를 설정하는 방법에 대해서 살펴봤습니다.
> 우리는 또한 도커 이미지를 빌드하기 위해서 Dockerfile을 생성하였습니다.
> 그리고나서 우리는 우리의 이미지에 태그하고 삭제해봤습니다.
> 다음 모듈에서는 우리는 컨테이너를 실행하는 방법에 대해서 보도록 하겠습니다.


