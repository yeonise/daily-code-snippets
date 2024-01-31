# Best practices for writing Dockerfiles

This topic covers recommended best practices and methods for building efficient images.

> 이 주제는 효율적인 이미지들을 빌드하기 위한 권장하는 모범 사례와 방법들을 다룹니다.

Docker builds images automatically by reading the instructions from a Dockerfile -- a text file that contains all
commands, in order, needed to build a given image. A Dockerfile adheres to a specific format and set of instructions
which you can find at [Dockerfile reference](https://docs.docker.com/engine/reference/builder/).

> 도커는 이미지들을 Dockerfile(Dockerfile은 주어진 이미지를 빌드하기 위해서 필요한 것들과 순서, 명령어들을 포함하는 텍스트 파일)로부터 명령어들을 읽어서 자동적으로 빌드합니다.
> Dockerfile은 Dockerfile 참조에서 찾을 수 있는 특정한 형식과 명령어들을 준수합니다.

A Docker image consists of read-only layers each of which represents a Dockerfile instruction. The layers are stacked
and each one is a delta of the changes from the previous layer. The following is the contents of an example Dockerfile:

> 도커 이미지는 Dockerfile 명령어들을 나타내는 읽기 전용의 레이어로 각각 구성되어 있습니다.
> 레이어들은 스택형식으로 쌓여있고 각각의 레이어는 이전 레이어로부터의 변경 사항들의 차이입니다.
> 다음은 Dockerfile 예제의 내용입니다.

```
# syntax=docker/dockerfile:1

FROM ubuntu:22.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

Each instruction creates one layer:

> 각각의 명령어들은 하나의 레이어를 생성합니다.

- `FROM` creates a layer from the `ubuntu:22.04` Docker image.
- `COPY` adds files from your Docker client's current directory.
- `RUN` builds your application with `make`.
- `CMD` specifies what command to run within the container.

> - `FROM` 명령어는 `ubuntu:22.04` 도커 이미지로부터 레이어를 생성합니다.
> - `COPY` 명령어는 도커 클라이언트의 현재 디렉토리부터 파일들을 추가합니다.
> - `RUN` 명령어는 `make` 명령어를 가지고 애플리케이션을 빌드합니다.
> - `CMD` 명령어는 컨테이너 내에서 실행하기 위한 명령어를 명세합니다.

When you run an image and generate a container, you add a new writable layer, also called the container layer, on top of
the underlying layers. All changes made to the running container, such as writing new files, modifying existing files,
and deleting files, are written to this writable container layer.

> 여러분이 컨테이너를 생성하고 이미지를 실행할때 여러분들은 작성가능한 새로운 레이러를 추가합니다. 기본 계층 위에 컨테이너 계층이라고 부르는 새로운 작성가능한 레이어를 추가합니다.
> 모든 변경사항들은 새로운 파일 작성, 존재하는 파일 수정하기, 파일들을 삭제하는 것과 같은 실행중인 컨테이너에 만들어지는 것들은 작성가능한 컨테이너 레이어에 작성된다.

For more on image layers and how Docker builds and stores images,
see [About storage drivers](https://docs.docker.com/storage/storagedriver/).

> 이미지 레이어 및 도커에서 이미지를 빌드하고 저장하는 방법에 대한 자세한 내용은 저장소 드라이버 정보를 참조해주세요.

## 일반적인 가이드라인과 권장사항들(General guidelines and recommendations)

### 사용후 삭제할 컨테이너 생성하기(Create ephemeral containers)

The image defined by your Dockerfile should generate containers that are as ephemeral as possible. Ephemeral means that
the container can be stopped and destroyed, then rebuilt and replaced with an absolute minimum set up and configuration.

> Dockerfile에서 정의한 이미지는 가능한 한 일시적인 컨테이너를 생성해야 합니다.
> 일시적이란 컨테이너를 중지하고 파기한 다음에 다시 작성하여 절대적으로 최소 설정 및 구성으로 교체할 수 있습니다.

Refer to [Processesopen_in_new](https://12factor.net/processes) under The Twelve-factor App methodology to get a feel
for the motivations of running containers in such a stateless fashion.

> 이러한 무상태 저장 방식으로 컨테이너를 실행하는 동기에 대해서 알아보려면 Twelve-factor App 방론 아래 Processopen_in_new를 참고해주세요

### 빌드 컨텍스트 이해하기(Understand build context)

See [Build context](https://docs.docker.com/build/building/context/) for more information.

> 더 많은 정보를 보기 위해서 빌드 컨텍스트를 참고해주세요

### 표준 입력을 통해서 Dockerfile을 파이프하기(Pipe Dockerfile through stdin)

Docker has the ability to build images by piping a Dockerfile through stdin with a local or remote build context. Piping
a Dockerfile through stdin can be useful to perform one-off builds without writing a Dockerfile to disk, or in
situations where the Dockerfile is generated, and should not persist afterwards.

> 원격 빌드 컨텍스트나 로컬에서 표준 입력을 통해 Dockerfile을 파이프함으로써 이미지들을 빌드하기 위한 능력을 도커는 가지고 있습니다.
> Dockerfile을 표준입력을 통해서 파이프 하는 것은 Dockerfile을 디스크에 저장하지 않고 또는 Dockerfile이 생성되는 상황에서 일회성 빌드를 수행하는데 유용할 수 있으며, 이후에도 지속되지
> 않아야 합니다.


Note

The examples in the following sections use here documentsopen_in_new for convenience, but any method to provide the
Dockerfile on stdin can be used.

> 참고
>
> 다음 섹션들의 예제들은 편의상 여기 documentsopen_in_new를 사용하지만 표준 입력에서 Dockerfile을 제공하는 모든 방을 사용할 수 있습니다.

For example, the following commands are equivalent:

> 예를 들어 다음 명령어들도 동일한 것들입니다.

```
echo -e 'FROM busybox\nRUN echo "hello world"' | docker build -
```

```
docker build -<<EOF
FROM busybox
RUN echo "hello world"
EOF
```

You can substitute the examples with your preferred approach, or the approach that best fits your use case.

> 여러분들은 예제들을 여러분들이 선호하는 방법으로 대체할 수 있거나 여러분들의 사용하는 것에 맞게 맞출 수 있습니다.'

### 빌드 컨텍스트를 전송하는 것 없이 표준 입력으로부터 Dockerfile을 사용하여 이미지를 빌드하기(Build an image using a Dockerfile from stdin, without sending build context)

Use this syntax to build an image using a Dockerfile from stdin, without sending additional files as build context. The
hyphen (-) takes the position of the PATH, and instructs Docker to read the build context, which only contains a
Dockerfile, from stdin instead of a directory:

> 빌드 컨텍스트로서 추가적인 파일들을 전송없이 표준 입력으로부터 DOckerfile을 사용하여 이미지를 빌드하기 위해서 이 문법을 사용하세요.
> 하이폰(-)은 PATH의 위치를 의미하며, 디렉토리 대신 표준 입력에서 Dockerfile만 포함된 빌드 컨텍스트를 읽도록 Docker에게 지시합니다.

```shell
docker build [OPTIONS] -
```

The following example builds an image using a Dockerfile that is passed through stdin. No files are sent as build
context to the daemon.

> 다음 예제는 표준 입력을 통해 전달된 Dockerfile을 사용하여 이미지를 빌드합니다.
> 파일들은 데몬에게 빌드 컨텍스트로서 전송되지 않습니다.

```
docker build -t myimage:latest -<<EOF
FROM busybox
RUN echo "hello world"
EOF
```

Omitting the build context can be useful in situations where your Dockerfile doesn't require files to be copied into the
image, and improves the build speed, as no files are sent to the daemon.

> 빌드 컨텍스트를 생략하는 것은 여러분들의 Dockerfile이 이미지로 파일들을 복사하는 것을 요구하지 않는 상황에서 유용할 수 있습니다.
> 그리고 파일들이 데몬으로 전송되지 않기 때문에 빌드 속도도 개선할 수 있습니다.

If you want to improve the build speed by excluding some files from the build context, refer to exclude with
[.dockerignore](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#exclude-with-dockerignore).

> 만약 여러분들이 빌드 컨텍스트에서 몇몇 파일들을 제외함으로써 빌드 속도를 빠르게 하기를 원하다면 .dockerignore를 가지고 제외하는 것을 참고해보세요.

Note

If you attempt to build an image using a Dockerfile from stdin, without sending a build context, then the build will
fail if you use COPY or ADD. The following example illustrates this:

> 참고
> 만약 여러분들이 표준 입력에서 Dockerfile을 사용하여 이미지를 빌드하기 위해 시도한다면 만약 여러분들이 COPY 또는 ADD를 사용한다면 실패할 것입니다.
> 다음 예제는 그러한 상황에 대한 예제입니다.

```
# create a directory to work in
mkdir example
cd example

# create an example file
touch somefile.txt

docker build -t myimage:latest -<<EOF
FROM busybox
COPY somefile.txt ./
RUN cat /somefile.txt
EOF

# observe that the build fails
...
Step 2/3 : COPY somefile.txt ./
COPY failed: stat /var/lib/docker/tmp/docker-builder249218248/somefile.txt: no such file or directory
```

### 표준 입력에서 Dockerfile을 사용하여 원격 빌드 컨텍스트에서 빌드하세요.(Build from a remote build context, using a Dockerfile from stdin)

Use this syntax to build an image using files from a remote Git repository, using a Dockerfile from stdin. The syntax
uses the `-f` (or `--file`) option to specify the Dockerfile to use, using a hyphen (`-`) as filename to instruct Docker
to read the Dockerfile from stdin:

> 표준 입력에서 Dockerfile을 사용하여 원격 깃 저장소에서 파일들을 사용하여 이미지를 빌드하기 위해서 이 문법을 사용하세요.
> 문법은 `-f`(또는 `--file`) 옵션을 사용하여 사용할 도커 파일을 지정하고, 파일 이름으로 하이폰을 사용하여 도커가 표준 입력에서 도커 파일을 읽도록 지시합니다.

```
docker build [OPTIONS] -f- PATH
```

This syntax can be useful in situations where you want to build an image from a repository that doesn't contain a
Dockerfile, or if you want to build with a custom Dockerfile, without maintaining your own fork of the repository.

> 이 문법은 여러분들이 Dockerfile을 포함하지 않는 저장소에서 이미지를 빌드하기를 원하는 상황에서 유용할 수 있습니다.
> 또는 만약에 여러분들이 가지고 있는 저장소의 포크로 관리하는 것 없이 커스텀한 Dockerfile을 가지고 빌드하기를 원할때 유용합니다.

The example below builds an image using a Dockerfile from stdin, and adds the hello.c file from the
hello-worldopen_in_new repository on GitHub.

> 표준 입력에서 Dockerfile을 사용하여 이미지를 빌드하는 예제입니다. 그리고 hello.c 파일을 깃허브에 hello-worldopen_in_new 저장소에 추가합니다.

```
docker build -t myimage:latest -f- https://github.com/docker-library/hello-world.git <<EOF
FROM busybox
COPY hello.c ./
EOF
```

Note

When building an image using a remote Git repository as the build context, Docker performs a git clone of the repository
on the local machine, and sends those files as the build context to the daemon. This feature requires you to install Git
on the host where you run the docker build command.

> 참고
>
> 빌드 컨텍스트로서 원격 깃 저장소를 사용하여 이미지를 빌드할때 도커는 저장소의 git clone를 로컬 머신에서 수행합니다.
> 그리고 데몬에 빌드 컨텍스트로서 이러한 파일들을 전송합니다.
> 이 기능은 도커 빌드 명령어 수행시에 여러분들의 호스트 머신에 깃 설치를 요구합니다.

### .dockerignore를 가지고 제외하기(Exclude with .dockerignore)

To exclude files not relevant to the build, without restructuring your source repository, use a .dockerignore file. This
file supports exclusion patterns similar to .gitignore files. For information on creating one,
see [.dockerignore file](https://docs.docker.com/engine/reference/builder/#dockerignore-file).

> 여러분들의 소스 저장소를 재구성 없이 빌드와 관련 없는 파일들을 제외하기 위해서는 .dockerignore 파일을 사용하세요.
> .dockerignore 파일은 .gitignore 파일과 비슷하게 제외 패턴을 지원합니다.
> 더 자세한 정보는 .dockerignore file을 참고해주세요

### 멀티 스테이지 빌드 사용하기(Use multi-stage builds)

[Multi-stage builds](https://docs.docker.com/build/building/multi-stage/) allow you to drastically reduce the size of
your final image, without struggling to reduce the number of intermediate layers and files.

> 멀티 스테이지 빌드를 사용하면 중간 계층 및 파일의 수를 줄이는 수고 없이 최종 이미지의 크기를 크게 줄일 수 있습니다.

Because an image is built during the final stage of the build process, you can minimize image layers by leveraging build
cache.

> 빌드 프로세스의 마지막 스테이지 동안 이미지가 빌드되기 때문에 여러분들은 빌드 캐시를 활용하여 이미지 레이어들을 최소화시킬 수 있습니다.

For example, if your build contains several layers and you want to ensure the build cache is reusable, you can order
them from the less frequently changed to the more frequently changed. The following list is an example of the order of
instructions:

> 예를 들어 만약 여러분들의 빌드가 여러개의 레이어들을 포함하고 여러분들이 빌드 캐시를 재사용하기를 원한다면 여러분들은 변경의 빈도가 낮은 계층에서 변경 빈도가 높은 계층으로 순서를 지정할 수 있습니다.
> 다음 리스트는 명령어들의 순서의 예제입니다.

1. Install tools you need to build your application

2. Install or update library dependencies

3. Generate your application

> 1. 애플리케이션을 빌드하기 위해서 필요한 툴을 설치
> 2. 의존성 라이브러리들을 설치하거나 업데이트
> 3. 애플리케이션 생성

A Dockerfile for a Go application could look like:

> Go 애플리케이션에 대한 Dockerfile은 다음과 같을 수 있습니다.

```Dockerfile
# syntax=docker/dockerfile:1
FROM golang:1.20-alpine AS build

# Install tools required for project
# Run `docker build --no-cache .` to update dependencies
RUN apk add --no-cache git

# List project dependencies with go.mod and go.sum
# These layers are only re-built when Gopkg files are updated
WORKDIR /go/src/project/
COPY go.mod go.sum /go/src/project/
# Install library dependencies
RUN go mod download

# Copy the entire project and build it
# This layer is rebuilt when a file changes in the project directory
COPY . /go/src/project/
RUN go build -o /bin/project

# This results in a single layer image
FROM scratch
COPY --from=build /bin/project /bin/project
ENTRYPOINT ["/bin/project"]
CMD ["--help"]
```

## 필수적이지 않은 패키지들을 설치하지 마세요(Don't install unnecessary packages)

Avoid installing extra or unnecessary packages just because they might be nice to have. For example, you don’t need to
include a text editor in a database image.

> 가지고 있는 것이 좋을 수도 있다는 것만으로 불필요한 패키지를 추가하지 마세요.
> 예를 들어 데이터베이스 이미지에 텍스트 에디터를 포함할 필요는 없는 것 처럼요.

When you avoid installing extra or unnecessary packages, your images will have reduced complexity, reduced dependencies,
reduced file sizes, and reduced build times.

> 불필요한 패키지 설치를 하지 않을 때 이미지는 복잡성과 의존성이 줄어들 것입니다. 또한 파일 크기가 줄어들고 빌드 시간도 단축될 것입니다.

### 디커플링 애플리케이션(Decouple applications)

Each container should have only one concern. Decoupling applications into multiple containers makes it easier to scale
horizontally and reuse containers. For instance, a web application stack might consist of three separate containers,
each with its own unique image, to manage the web application, database, and an in-memory cache in a decoupled manner.

> 각각의 컨테이너는 오직 하나의 관심사를 가져야만 합니다. 애플리케이션을 여러 컨테이너로 분리하면 수평 확장 및 컨테이너 재사용이 쉬워집니다.
> 예를 들어 웹 애플리케이션 스택은 웹 애플리케이션, 데이터베이스 인 메모리 캐를 분리된 방식으로 관리하기 위해서 유니크한 이미지가 있는 3개의 개별적인 컨테이너로 구성할 수 있습니다.

Limiting each container to one process is a good rule of thumb, but it's not a hard and fast rule. For example, not only
can containers be spawned with an init process, some programs might spawn additional processes of their own accord. For
instance, Celeryopen_in_new can spawn multiple worker processes, and Apacheopen_in_new can create one process per
request.

> 각각의 컨테이너를 하나의 프로세스로 제한하는 것은 좋은 규칙입니다. 그러나 어렵고 빠른 규칙은 아닙니다. 예를 들어 init 프로세스로 컨테이너를 생성할 수 있을 뿐만 아니라 일부 프로그램은 자체적으로 추가
> 프로세스를 생성할 수도 있습니다.
> 예를 들어 Celeryopen_in_new는 여러개의 작업자 프로세스를 생성할 수 있고, Apacheopen_in_new는 한 요청당 하나의 프로세스를 생성할 수 있습니다.

Use your best judgment to keep containers as clean and modular as possible. If containers depend on each other, you can
use Docker container networks to ensure that these containers can communicate.

> 최선의 판단으로 컨테이너들을 가능한 한 클린하고 모듈식으로 유지하도록 하세요.
> 컨테이너가 서로 종속된 경우 도커 컨테이너 네트워크를 사용하여 이러한 컨테이너가 통신할 수 있는지 확인할 수 있습니다.

## Minimize the number of layers

In older versions of Docker, it was important that you minimized the number of layers in your images to ensure they were
performant. The following features were added to reduce this limitation:

> 도커의 예전 버전에서 도커는 이미의 레이어 수를 최소화하여 레이어 수를 확인하는 것이 성능에 중요했습니다.
> 다음 기능은 이러한 한계를 줄이기 위해 추가된 기능들입니다.

Here’s an example from the buildpack-deps image:

> 여기 buildpack-deps 이미지에 대한 예제입니다.

```shell
RUN apt-get update && apt-get install -y \
  bzr \
  cvs \
  git \
  mercurial \
  subversion \
  && rm -rf /var/lib/apt/lists/*
```

### 빌드 캐시 링크 활용(Leverage build cache link)

When building an image, Docker steps through the instructions in your Dockerfile, executing each in the order specified.
As each instruction is examined, Docker looks for an existing image in its cache, rather than creating a new, duplicate
image.

> 이미지를 빌드할 때 도커는 Dockerfile에 있는 명령어들을 순서대로 실행합니다. 각 명령을 검사할 때 도커는 새 중복 이미지를 만드는 대신 캐시에서 기존 이미지를 찾습니다.






