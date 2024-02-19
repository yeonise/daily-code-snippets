## 목차
- [Dockerfile들을 작성하기 위한 모범 사례의 개요](#dockerfile들을-작성하기-위한-모범-사례의-개요overview-of-best-practices-for-writing-dockerfiles)
	- [Dockerfile은 무엇인가?](#dockerfile은-무엇인가what-is-a-dockerfile)
- [일반적인 가이드라인과 권장사항들](#일반적인-가이드라인과-권장사항들general-guidelines-and-recommendations)
	- [멀티 스테이지 빌드 사용하기](#멀티-스테이지-빌드-사용하기use-multi-stage-builds)
	- [.dockerignore를 가지고 제외하기](#dockerignore를-가지고-제외하기exclude-with-dockerignore)
	- [필수적이지 않은 패키지들을 설치하지 마세요](#필수적이지-않은-패키지들을-설치하지-마세요dont-install-unnecessary-packages)
	- [애플리케이션을 디커플링하라](#애플리케이션을-디커플링하라decouple-applications)
	- [멀티-라인 매개변수들을 정렬하라](#멀티-라인-매개변수들을-정렬하라sort-multi-line-arguments)
	- [빌드 캐시 링크 활용](#빌드-캐시-링크-활용leverage-build-cache-link)
	- [베이스 이미지 버전들을 고정하라](#베이스-이미지-버전들을-고정하라pin-base-image-versions)
- [Dockerfile 명령어들에 대한 모범 사례](#dockerfile-명령어들에-대한-모범-사례best-practices-for-dockerfile-instructions)
	- [FROM](#from)
	- [LABEL](#label)
	- [RUN](#run)
	- [CMD](#cmd)
	- [EXPOSE](#expose)
	- [ENV](#env)

## Dockerfile들을 작성하기 위한 모범 사례의 개요(Overview of best practices for writing Dockerfiles)
This topic covers recommended best practices and methods for building efficient images. It provides [general guidelines for your Dockerfiles](https://docs.docker.com/develop/develop-images/guidelines/) and more [specific best practices for each Dockerfile instruction](https://docs.docker.com/develop/develop-images/instructions/).

> 이 주제는 효율적인 이미지들을 빌드하기 위한 방법과 권장사항을 다룹니다. 이 권장사항은 여러분들의 Dockerfile들에 대해서 일반적인 가이드라인을 제공하고 각각의 Dockerfile 명령어에 대해서 더 특정한 권장사항을 제공합니다.

### Dockerfile은 무엇인가?(What is a Dockerfile?)
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

### .dockerignore를 가지고 제외하기(Exclude with .dockerignore)

To exclude files not relevant to the build, without restructuring your source repository, use a .dockerignore file. This
file supports exclusion patterns similar to .gitignore files. For information on creating one,
see [.dockerignore file](https://docs.docker.com/engine/reference/builder/#dockerignore-file).

> 여러분들의 소스 저장소를 재구성 없이 빌드와 관련 없는 파일들을 제외하기 위해서는 .dockerignore 파일을 사용하세요.
> .dockerignore 파일은 .gitignore 파일과 비슷하게 제외 패턴을 지원합니다.
> 더 자세한 정보는 .dockerignore file을 참고해주세요


### 사용후 삭제할 컨테이너 생성하기(Create ephemeral containers)

The image defined by your Dockerfile should generate containers that are as ephemeral as possible. Ephemeral means that
the container can be stopped and destroyed, then rebuilt and replaced with an absolute minimum set up and configuration.

> Dockerfile에서 정의한 이미지는 가능한 한 일시적인 컨테이너를 생성해야 합니다.
> 일시적이란 컨테이너를 중지하고 파기한 다음에 다시 작성하여 절대적으로 최소 설정 및 구성으로 교체할 수 있습니다.

Refer to [Processesopen_in_new](https://12factor.net/processes) under The Twelve-factor App methodology to get a feel
for the motivations of running containers in such a stateless fashion.

> 이러한 무상태 저장 방식으로 컨테이너를 실행하는 동기에 대해서 알아보려면 Twelve-factor App 방론 아래 Processopen_in_new를 참고해주세요

### 필수적이지 않은 패키지들을 설치하지 마세요(Don't install unnecessary packages)

Avoid installing extra or unnecessary packages just because they might be nice to have. For example, you don’t need to
include a text editor in a database image.

> 가지고 있는 것이 좋을 수도 있다는 것만으로 불필요한 패키지를 추가하지 마세요.
> 예를 들어 데이터베이스 이미지에 텍스트 에디터를 포함할 필요는 없는 것 처럼요.

When you avoid installing extra or unnecessary packages, your images will have reduced complexity, reduced dependencies,
reduced file sizes, and reduced build times.

> 불필요한 패키지 설치를 하지 않을 때 이미지는 복잡성과 의존성이 줄어들 것입니다. 또한 파일 크기가 줄어들고 빌드 시간도 단축될 것입니다.


### 애플리케이션을 디커플링하라(Decouple applications)

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

### 멀티-라인 매개변수들을 정렬하라(Sort multi-line arguments)

Whenever possible, sort multi-line arguments alphanumerically to make maintenance easier. This helps to avoid duplication of packages and make the list much easier to update. This also makes PRs a lot easier to read and review. Adding a space before a backslash (`\`) helps as well.

> 가능하면 멀티-라인 매개변수는 유지보수하기 쉽게 하기 위해서 알파벳순으로 정렬하라. 알파벳 순으로 정렬하는 것은 패키지의 중복을 피하고 업데이트를 쉽게 만들어준다. 또한 리뷰 및 읽기에서 PR을 쉽게 만든다. 백슬래시(`\`) 전에 공백을 추가하는 것도 도움이 됩니다.

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


The basic rules of build cache invalidation are as follows:
> 빌드 캐시 무효화의 기본적인 규칙은 다음과 같습니다.

- Starting with a parent image that's already in the cache, the next instruction is compared against all child images derived from that base image to see if one of them was built using the exact same instruction. If not, the cache is invalidated.
    
- In most cases, simply comparing the instruction in the Dockerfile with one of the child images is sufficient. However, certain instructions require more examination and explanation.
    
- For the `ADD` and `COPY` instructions, the modification time and size file metadata is used to determine whether cache is valid. During cache lookup, cache is invalidated if the file metadata has changed for any of the files involved.
    
- Aside from the `ADD` and `COPY` commands, cache checking doesn't look at the files in the container to determine a cache match. For example, when processing a `RUN apt-get -y update` command the files updated in the container aren't examined to determine if a cache hit exists. In that case just the command string itself is used to find a match.

> - 캐시에 이미 존재하는 부모 이미지를 시작으로 다음 명령어는 기본 이미지에서 파생된 모든 자식 이미지들과 비교하여 자식 이미지들중 하나가 정확히 동일한 명령어를 사용하여 빌드되었는지 확인합니다.
> - 대부분의 경우에 자식 이미지들이 가진 Dockerfile에서 명령어를 간단하게 비교하는 것이 효율적입니다. 그러나 특정한 명령어들은 더 많은 검사와 설명이 요구됩니다.
> - `ADD` 및 `COPY` 명령어의 경우 수정시간과 크기 파일 메타데이터가 캐시에 유효한지 판단하는데 사용됩니다.
> - `ADD` 및 `COPY` 명령어 외에 캐시 일치 여부를 확인하기 위한 캐시 확인은 컨테이너의 파일들을 보지 않습니다. 예를 들어 `RUN apt-get -y update` 명령어를 처리할때 컨테이너 안의 파일들은 캐시가 존재하지는지 검사하지 않습니다. 이 경우에 명령어 문자열 자체만 일치하는 항목을 찾는데 사용됩니다.

Once the cache is invalidated, all subsequent Dockerfile commands generate new images and the cache isn't used.

> 일단 캐시가 유효하지 않다면, 모든 Dockerfile 명령어들은 새로운 이미지를 생산합니다. 그리고 캐시는 사용되지 않습니다.

If your build contains several layers and you want to ensure the build cache is reusable, order the instructions from less frequently changed to more frequently changed where possible.

> 만약 여러분들의 빌드가 여러개의 레이어를 포함하고 빌드 캐시를 재사용하기를 원한다면 명령어들은 가능한 한 자주 변경하지 않는 것부터 자주 변경하는 순으로 놓으세요.

For more information about the Docker build cache and how to optimize your builds, see [cache management](https://docs.docker.com/build/cache/).

> 더 자세한 도커 빌드 캐시에 대한 정보와 빌드를 최적화하는 방법을 보고 싶다면 캐시 관리 링크를 참고하세요.

### 베이스 이미지 버전들을 고정하라(Pin base image versions)
Image tags are mutable, meaning a publisher can update a tag to point to a new image. This is a useful because it lets publishers update tags to point to newer versions of an image. And as an image consumer, it means you automatically get the new version when you re-build your image.

> 이미지 태그들은 변경이 가능하므로 게시자가 새 이미지를 가리키도록 태그를 업데이트할 수 있습니다. 게시자가 새로운 버전의 이미지를 가리키도록 태그를 업데이트할 수 있기 때문에 유용합니다. 이미지 소비자로써 이미지를 다시 재빌드할때 자동적으로 새로운 버전을 얻을 수 있음을 의미합니다.

For example, if you specify `FROM alpine:3.19` in your Dockerfile, `3.19` resolves to the latest patch version for `3.19`.

> 예를들어 만약 여러분들의 Dockerfile에 `FROM alpine:3.19`를 명세하였다면, `3.19`는 `3.19`에 대한 최신 패치 버전으로 해결됩니다.

```
# syntax=docker/dockerfile:1
FROM alpine:3.19
```

At one point in time, the `3.19` tag might point to version 3.19.1 of the image. If you rebuild the image 3 months later, the same tag might point to a different version, such as 3.19.4. This publishing workflow is best practice, and most publishers use this tagging strategy, but it isn't enforced.

> 이 시점에서 `3.19` 태그는 이미지의 3.19.1 버전을 가리킬 수 있습니다. 만약 여러분들이 3달 후에 이미지를 재빌드한다면, 같은 태그는 3.19.4와 같은 다른 버전을 가리킬 수 있습니다. 이 퍼블리싱 워크플로우는 권고하고 대부분의 게시자들이 이 태깅 전략을 사용합니다. 그러나 이 전략은 강제는 아닙니다.

The downside with this is that you're not guaranteed to get the same for every build. This could result in breaking changes, and it means you also don't have an audit trail of the exact image versions that you're using.

> 이 전략의 단점은 모든 빌드에 대해서 동일한 것을 보장하지 않다는 점입니다. 이로 인해 변경 사항이 발생할 수 있으며, 사용중인 정확한 이미지 버전에 대한 감사 기록도 없습니다.

To fully secure your supply chain integrity, you can pin the image version to a specific digest. By pinning your images to a digest, you're guaranteed to always use the same image version, even if a publisher replaces the tag with a new image. For example, the following Dockerfile pins the Alpine image to the same tag as earlier, `3.19`, but this time with a digest reference as well.

> 여러분들의 chain intergrity를 완벽하게 보호하기 위해서 이미지 버전을 특정한 다이제스트에 고정할 수 있습니다. 이미지들을 어떤 하나의 다이제스트에 고정함으로써 게시자가 새로운 이미지와 같이 태그를 교체해도 여러분들은 언제나 같은 이미지 버전을 사용하는 것을 보장받을 수 있습니다. 예를 들어 다음 Dockerfile은 Alpine 이미지를 이전과 동일한 태그인 `3.19`로 고정하지만 동일한 다이제스트를 참조합니다.

```
# syntax=docker/dockerfile:1
FROM alpine:3.19@sha256:13b7e62e8df80264dbb747995705a986aa530415763a6c58f84a3ca8af9a5bcd
```

With this Dockerfile, even if the publisher updates the `3.19` tag, your builds would still use the pinned image version: `13b7e62e8df80264dbb747995705a986aa530415763a6c58f84a3ca8af9a5bcd`.

> 게시자가 `3.19`태그를 업데이트하여도 여러분들의 빌드는 여전히 고정된 이미지 버전으로 사용합니다.

While this helps you avoid unexpected changes, it's also more tedious to have to look up and include the image digest for base image versions manually each time you want to update it. And you're opting out of automated security fixes, which is likely something you want to get.

> 이 전략은 여러분들이 예상치 못한 변경들을 피하는데 도움이 되는 반면, 여러분들이 업데이트를 원할 때마다 매 시간 직접 베이스 이미지 버전들에 대한 이미지 다이제스트를 포함한 것들을 살펴봐야하는 것은 지루합니다. 그리고 여러분들은 자동화된 보안 수정을 선택하지 않을 수 도 있습니다. 이는 여러분들이 원하는 것일 수도 있습니다.

Docker Scout has a built-in [**Outdated base images** policy](https://docs.docker.com/scout/policy/#outdated-base-images) that checks for whether the base image version you're using is in fact the latest version. This policy also checks if pinned digests in your Dockerfile correspond to the correct version. If a publisher updates an image that you've pinned, the policy evaluation returns a non-compliant status, indicating that you should update your image.

> Docker Scout은 여러분들이 사용하고 있는 베이스 이미지 버전이 최신 버전인지 아닌지 검사하는 구식의 베이스 이미지 정책들을 내장하고 있습니다. 이 정책은 또한 Dockerfile에 고정된 다이제스트들이 올바른 버전과 일치하는지 확인합니다. 만약 게시자가 여러분들이 고정한 이미지를 업데이트하였다면 정책 평가는 여러분들의 이미지를 업데이트 하는것을 알리는 non-comliant 상태를 반환합니다.

Docker Scout also supports an automated remediation workflow for keeping your base images up-to-date. When a new image digest is available, Docker Scout can automatically raise a pull request on your repository to update your Dockerfiles to use the latest version. This is better than using a tag that changes the version automatically, because you're in control and you have an audit trail of when and how the change occurred.

> Docker Scout은 또한 여러분들의 베이스 이미지들을 최신 상태로 유지하기 위한 자동화된 교정 워크플로우를 지원합니다. 새로운 이미지 다이제스트가 사용가능하게 되면 Docker Scout은 여러분들의 Dockerfile들을 최신 버전을 사용할 수 있도록 업데이트하기 위해서 자동적으로 여러분들의 리포지토리에 pull request를 발생시킵니다. 이것은 자동적으로 버전을 변경하는 태그를 사용하는 것보다 나을수 있습니다. 왜냐하면 여러분들은 제어하고 있고 변경이 언제 어떻게 이루어졌는지에 대한 감사 기록이 있기 때문입니다.

For more information about automatically updating your base images with Docker Scout, see [Remediation](https://docs.docker.com/scout/policy/remediation/#automatic-base-image-updates)

> Docker Scout을 가지고 베이스 이미지 자동 업데이트에 대한 더욱 자세한 정보는 Remediation을 참고해주세요

## Dockerfile 명령어들에 대한 모범 사례(Best practices for Dockerfile instructions)

These recommendations are designed to help you create an efficient and maintainable Dockerfile.

> 이 권장사항들은 여러분들에게 효율적이고 유지보수가능한 Dockerfile을 생성하는데 도움이 되도록 설계되었습니다.

### FROM
Whenever possible, use current official images as the basis for your images. Docker recommends the [Alpine image](https://hub.docker.com/_/alpine/) as it is tightly controlled and small in size (currently under 6 MB), while still being a full Linux distribution.

> 가능하면 여러분들의 베이스 이미지는 공식 이미지를 사용하세요. 도커는 완벽한 리눅스 배포판에도 불구하고 엄격하게 제어되고 크기가 작기 때문에 (현재 6MB 미만)을 권장합니다.

For more information about the `FROM` instruction, see [Dockerfile reference for the FROM instruction](https://docs.docker.com/engine/reference/builder/#from).

> `FROM` 명령어에 대한 자세한 정보는 FROM 명령어에 대한 Dockerfile reference를 참고해주세요.

### LABEL
You can add labels to your image to help organize images by project, record licensing information, to aid in automation, or for other reasons. For each label, add a line beginning with `LABEL` with one or more key-value pairs. The following examples show the different acceptable formats. Explanatory comments are included inline.

> 여러분들은 라이센스 정보를 기록하거나 자동화를 피하거나 다른 이유로 프로젝트의 이미지를 조직화하는데 도움이 되기 위해서 이미지에 레이블을 추가할 수 있습니다. 각각의 레이블에 대하여 한개 이상의 key-value 쌍을 가진 LABEL을 가지고 한줄 시작하는 것을 추가해보세요. 다음 예제는 서로 다른 수용할 수 있는 형식들을 보여줍니다. 설명들이 인라인으로 포함되어 있습니다.

Strings with spaces must be quoted or the spaces must be escaped. Inner quote characters (`"`), must also be escaped. For example:

> 공백들을 가진 문자열 값들은 쌍따옴표로 감싸거나 이스케이프(`\`) 문자로 대체되어야 합니다. 내부 문자열에 쌍따옴표(") 문자 또한 앞에 이스케이프 문자가 있어야 합니다. 예를 들어 다음과 같습니다.

```dockerfile
# Set one or more individual labels
LABEL com.example.version="0.0.1-beta"
LABEL vendor1="ACME Incorporated"
LABEL vendor2=ZENITH\ Incorporated
LABEL com.example.release-date="2015-02-12"
LABEL com.example.version.is-production=""
```

An image can have more than one label. Prior to Docker 1.10, it was recommended to combine all labels into a single `LABEL` instruction, to prevent extra layers from being created. This is no longer necessary, but combining labels is still supported. For example:

> 한 이미지는 한개이상의 레이블을 가질 수 있습니다. Docker 1.10 전에는 추가적인 레이어들이 생성되는 것을 방지하기 위해서 모든 레이블들을 단일 `LABEL` 명령어로 결합하는 것이 권장되었습니다. 예를 들어 다음과 같이 하나의 LABEL 명령어를 사용하고 그 뒤에 공백으로 구분하여 하나의 라인으로 구성하였습니다.

```dockerfile
# Set multiple labels on one line
LABEL com.example.version="0.0.1-beta" com.example.release-date="2015-02-12"
```

The above example can also be written as:

> 위 예제는 또한 다음과 같이 작성할 수 있습니다.

```dockerfile
# Set multiple labels at once, using line-continuation characters to break long lines
LABEL vendor=ACME\ Incorporated \
      com.example.is-beta= \
      com.example.is-production="" \
      com.example.version="0.0.1-beta" \
      com.example.release-date="2015-02-12"
```

See [Understanding object labels](https://docs.docker.com/config/labels-custom-metadata/) for guidelines about acceptable label keys and values. For information about querying labels, refer to the items related to filtering in [Managing labels on objects](https://docs.docker.com/config/labels-custom-metadata/#manage-labels-on-objects). See also [LABEL](https://docs.docker.com/engine/reference/builder/#label) in the Dockerfile reference.

> 허용할 수 있는 레이블 Key값들과 Value값들에 대하여 가이드라인들에 대하여 객체 레이블 이해를 참고해주세요. 레이블 조회에 대한 자세한 내용은 객체의 레이블 관리의 필터링 관련 항목들을 참고해주세요. Dockerfile 참조의 LABEL도 참고해주세요.

### RUN
Split long or complex `RUN` statements on multiple lines separated with backslashes to make your Dockerfile more readable, understandable, and maintainable.

> 백슬래시(`\`)로 구분된 여러 줄에 길고 복잡한 `RUN` 문을 분할하여 Dockerfile을 더 읽기 쉽고 이해하기 쉬우며 유지 관리할 수 있습니다.

For more information about `RUN`, see [Dockerfile reference for the RUN instruction](https://docs.docker.com/engine/reference/builder/#run).

> `RUN`에 대한 더 자세한 정보는 RUN 명령어에 대한 Dockerfile 참조해주세요

#### apt-get
Probably the most common use case for `RUN` is an application of `apt-get`. Because it installs packages, the `RUN apt-get` command has several counter-intuitive behaviors to look out for.

> 대부분의 공통적인  사용 사례에서 `RUN`은 `apt-get` 의 애플리케이션일 것입니다. 왜냐하면 apt-get은 패키지들을 설치하기 때문에 RUN apt-get 명령어는 여러개의 반 직관적인 동작을 주의해야 합니다.

Always combine `RUN apt-get update` with `apt-get install` in the same `RUN` statement. For example:

> 언제나 `RUN apt-get update` 와 `apt-get install`은 같은 RUN 명령어에 결합되어 있습니다. 예를 들어 다음과 같습니다.

```dockerfile
RUN apt-get update && apt-get install -y \
    package-bar \
    package-baz \
    package-foo  \
    && rm -rf /var/lib/apt/lists/*
```

Using `apt-get update` alone in a `RUN` statement causes caching issues and subsequent `apt-get install` instructions to fail. For example, this issue will occur in the following Dockerfile:

> `apt-get update`를 RUN 명령어에서 혼자 사용하는 것은 이슈들을 캐싱하는 것과 부차적인 `apt-get install` 명령어들을 실패할 수 있습니다. 예를 들어, 이 이슈느는 다음 Dockerfile에서 발생합니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM ubuntu:22.04
RUN apt-get update
RUN apt-get install -y curl nginx
```

Docker sees the initial and modified instructions as identical and reuses the cache from previous steps. As a result the `apt-get update` isn't executed because the build uses the cached version. Because the `apt-get update` isn't run, your build can potentially get an outdated version of the `curl` and `nginx` packages.

> Docker는 초기의 명령과 수정된 명령을 동일한 것으로 보고 이전 단계의 캐시를 재사용합니다. 결과적으로 빌드가 캐시가 된 버전을 사용하기 때문에 apt-get update가 실행되지 않습니다. apt-get update가 실행되지 않기 때문에 빌드가 nginx 패키지의 오래된 버전을 가져올 수 있습니다.

Using `RUN apt-get update && apt-get install -y` ensures your Dockerfile installs the latest package versions with no further coding or manual intervention. This technique is known as cache busting. You can also achieve cache busting by specifying a package version. This is known as version pinning. For example:

> `RUN apt-get update && apt-get install -y`를 사용하는 것은 여러분들의 Dockerfile이 더이상의 코딩이나 수동 개입없이 최신 패키지 버전을 설치할 수 있습니다.
> 이 기술은 캐시 버스팅으로 알려져 있습니다. 여러분들은 또한 패키지 버전을 명세함으로써 캐시 버스팅을 기록할 수 있습니다. 이것은 버전 고정으로 알려져 있습니다. 예를 들어 다음과 같습니다.

```dockerfile
RUN apt-get update && apt-get install -y \
    package-bar \
    package-baz \
    package-foo=1.3.*
```

Version pinning forces the build to retrieve a particular version regardless of what’s in the cache. This technique can also reduce failures due to unanticipated changes in required packages.

> 버전 고정은 캐시에 있는 것과 관계없이 빌드가 특정 버전을 검색하도록 강제합니다. 이 기술은 또한 요구되는 패키지들의 예상치 못한 변경으로 인한 실패를 줄일 수 있습니다.

Below is a well-formed `RUN` instruction that demonstrates all the `apt-get` recommendations.

> 아래는 모든 `apt-get` 권장사항들을 보여주는 잘 만든 `RUN` 명령어입니다.

```dockerfile
RUN apt-get update && apt-get install -y \
    aufs-tools \
    automake \
    build-essential \
    curl \
    dpkg-sig \
    libcap-dev \
    libsqlite3-dev \
    mercurial \
    reprepro \
    ruby1.9.1 \
    ruby1.9.1-dev \
    s3cmd=1.1.* \
 && rm -rf /var/lib/apt/lists/*
```

The `s3cmd` argument specifies a version `1.1.*`. If the image previously used an older version, specifying the new one causes a cache bust of `apt-get update` and ensures the installation of the new version. Listing packages on each line can also prevent mistakes in package duplication.

> `s3cmd` 매개변수는 version `1.1.*` 으로 명세되어 있습니다. 만약 이미지가 이전에 예전 버전으로 사용되었던 적이 있고, 새 버전을 지정하면 `apt-get update`의 캐시 버스트가 발생하고 설치가 보장됩니다.

In addition, when you clean up the apt cache by removing `/var/lib/apt/lists` it reduces the image size, since the apt cache isn't stored in a layer. Since the `RUN` statement starts with `apt-get update`, the package cache is always refreshed prior to `apt-get install`.

> 게다가 여러분들이 `/var/lib/apt/lists`를 제거함으로써 apt cache를 제거할 때 이미지 사이즈는 줄어들 것입니다. apt cache는 레이어에 저장되지 않기 때문입니다. RUN 명령어는 apt-get update로 시작하기 때문에 패키지 캐시는 언제나 apt-get install 전에 실행되어 갱신됩니다.

Official Debian and Ubuntu images [automatically run `apt-get clean`](https://github.com/moby/moby/blob/03e2923e42446dbb830c654d0eec323a0b4ef02a/contrib/mkimage/debootstrap#L82-L105), so explicit invocation is not required.

> 공식적인 Debian과 Ubuntu 이미지들은 자동적으로 run apt-get clean을 실행합니다. 그래서 따라서 명시적인 호출이 필요하지 않습니다.

### Using pipes
Some `RUN` commands depend on the ability to pipe the output of one command into another, using the pipe character (`|`), as in the following example:

> 몇몇 `RUN` 명령어들은 다음 예제와 같이 파이프 문자(`|`)를 사용하여 한 명령어의 출력을 다른 명령어의 입력으로 파이프하는 기능에 따라 달라집니다.

```
RUN wget -O - https://some.site | wc -l > /number
```

Docker executes these commands using the `/bin/sh -c` interpreter, which only evaluates the exit code of the last operation in the pipe to determine success. In the example above, this build step succeeds and produces a new image so long as the `wc -l` command succeeds, even if the `wget` command fails.

> Docker는 `/bin/sh -c` 인터프리터를 사용하여 이 명령어들을 실행하며, 이 인터프리터는 파이프의 마지막 작업 종료 코드만 평가하여 성공 여부를 결정합니다. 이 빌드 단계는 `wc -l` 명령이 성공하는 한 `wget` 명령이 실패하더라도 새 이미지를 성공합니다.

If you want the command to fail due to an error at any stage in the pipe, prepend `set -o pipefail &&` to ensure that an unexpected error prevents the build from inadvertently succeeding. For example:

> 만약 여러분들이 파이프에서 어떤 단게에서 에러가 발생 때문에 명령어가 실패하기를 원한다면 `set -o pipefail &&`를 접두사로 추가합니다. 이것은 예상치 못한 에러 방지합니다.

```
RUN set -o pipefail && wget -O - https://some.site | wc -l > /number
```

>**Note**
>
>Not all shells support the `-o pipefail` option.
>
>In cases such as the `dash` shell on Debian-based images, consider using the _exec_ form of `RUN` to explicitly choose a shell that does support the `pipefail` option. For example:
>
RUN ["/bin/bash", "-c", "set -o pipefail && wget -O - https://some.site | wc -l > /number"]

> 노트
> 
> 모든 쉘들이 `-o pipefail` 옵션을 지원하지는 않습니다.
> 
> Debian 기반의 이미지인 `bash` 쉘같은 경우에는 `pipefail` 옵션을 지원하는 쉘을 명시적으로 선택하기 위해서 `RUN` 의 exec 폼을 사용하는 것을 고려하세요. 예를 들어 다음과 같습니다.
> 
> RUN ["/bin/bash", "-c", "set -o pipefail && wget -O - https://some.site | wc -l > /number"]

### CMD
The `CMD` instruction should be used to run the software contained in your image, along with any arguments. `CMD` should almost always be used in the form of `CMD ["executable", "param1", "param2"]`. Thus, if the image is for a service, such as Apache and Rails, you would run something like `CMD ["apache2","-DFOREGROUND"]`. Indeed, this form of the instruction is recommended for any service-based image.

> `CMD` 명령어는 이미지안에 포함된 소프트웨어를 몇몇 매개변수와 함께 전달해 실행시키기 위해서 사용되어야 합니다. `CMD`는 대개 `CMD ["executable", "param1", "param2"]`와 같은 형식으로 언제나 사용됩니다. 그러므로 만약 이미지가 Apache와 Rails와 같은 서비스용이라면, `CMD ["apache2", '-DFOREGROUND"]`와 같은 형식으로 실행하세요. 실제로 이러한 CMD 명령어의 형식은 모든 서비스 기반 이미지에 권장됩니다.

In most other cases, `CMD` should be given an interactive shell, such as bash, python and perl. For example, `CMD ["perl", "-de0"]`, `CMD ["python"]`, or `CMD ["php", "-a"]`. Using this form means that when you execute something like `docker run -it python`, you’ll get dropped into a usable shell, ready to go. `CMD` should rarely be used in the manner of `CMD ["param", "param"]` in conjunction with [`ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint), unless you and your expected users are already quite familiar with how `ENTRYPOINT` works.

> 대부분의 경우에 `CMD` 명령어에는 bash, python, perl과 같은 상호작용하는 쉘이 주어져야 합니다. 예를 들어 `CMD ["perl", "-de0"], CMD ["python"], CMD["php", "-a"]`와 같습니다. 이러한 형식을 사용하는 것은 여러분들이 `docker run -it python`을 실행하는 것과 같은 의미입니다. 이 명령어를 실행하면 여러분들을 명령어 쉘로 이동시킬 수 것입니다. `CMD`는 ENTRYPOINT와 함께 `CMD ["param", "param"]`의 방식으로 사용되는 경우가 거의 없습니다. 여러분과 여러분들의 예상되는 사용자들이 이미 ENTRYPOINT가 어떻게 작동하는지 익숙하지 않는 한 말이죠.

For more information about `CMD`, see [Dockerfile reference for the CMD instruction](https://docs.docker.com/engine/reference/builder/#cmd).

> `CMD`에 대한 자세한 설명은 CMD 명령어에 대한 Dockerfile 참조를 확인해주세요.

### EXPOSE
The `EXPOSE` instruction indicates the ports on which a container listens for connections. Consequently, you should use the common, traditional port for your application. For example, an image containing the Apache web server would use `EXPOSE 80`, while an image containing MongoDB would use `EXPOSE 27017` and so on.

> `EXPOST` 명령어는 컨테이너 연결 포트를 나타냅니다. 따라서 애플리케이션에 대한 공통적이고 전통적인 포트를 사용해야 합니다. 예를 들어 Apache web server를 포함하고 있는 이미지는 `EXPOST 80`을 사용합니다. 반면에 MongoDB를 포함하고 있는 이미지는 `EXPOST 27017`을 사용합니다.

For external access, your users can execute `docker run` with a flag indicating how to map the specified port to the port of their choice. For container linking, Docker provides environment variables for the path from the recipient container back to the source (for example, `MYSQL_PORT_3306_TCP`).

> 외부에서 접근을 하기 위해서 여러분들의 사용자들은 `docker run` 명령어를 그들이 선택한 특정한 포트 대 포트를 매핑하는 방법을 나타내는 플래그와 함께 실행합니다. 컨테이너를 링크하기 위해서는 Docker는 수신 컨테이너에서 소스로 돌아가는 경로에 대한 환경 변수를 제공합니다. (예를 들어 `MYSQL_PORT_3306_TCP`)

### ENV
To make new software easier to run, you can use `ENV` to update the `PATH` environment variable for the software your container installs. For example, `ENV PATH=/usr/local/nginx/bin:$PATH` ensures that `CMD ["nginx"]` just works.

> 새로운 소프트웨어를 쉽게 실행시키기 위해서 여러분들은 컨테이너에 설치된 소프트웨어에 대한 `PATH` 환경 변수를 업데이트하기 위해서 `ENV`를 사용할 수 있습니다. 예를 들어 `ENV` `PATH=/user/local/nginx/bin:$PATH`는 `CMD ["nginx"`]와 같이 동작하도록 합니다.

The `ENV` instruction is also useful for providing the required environment variables specific to services you want to containerize, such as Postgres’s `PGDATA`.

> `ENV` 명령어는 또한 Postgre's의 `PGDATA`와 같은 여러분들이 커스터마이징하기를 원하는 서비스에 요구되는 환경 변수들의 스펙을 제공하는데 유용합니다. 

Lastly, `ENV` can also be used to set commonly used version numbers so that version bumps are easier to maintain, as seen in the following example:

> 마지막으로 `ENV`는 유지보수를 쉽게하기 위한 버전 덤프할 수 있도록 공통적으로 사용되는 버전 번호들을 설정하기 위해서 사용될 수 있습니다. 다음 예제와 같습니다.

```dockerfile
ENV PG_MAJOR=9.3
ENV PG_VERSION=9.3.4
RUN curl -SL https://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgres && …
ENV PATH=/usr/local/postgres-$PG_MAJOR/bin:$PATH
```

Similar to having constant variables in a program, as opposed to hard-coding values, this approach lets you change a single `ENV` instruction to automatically bump the version of the software in your container.

 > 하드 코딩 값들이 아닌 프로그램의 일정한 변수가 있는것과 마찬가지로 이 방법을 사용하면 단일 `ENV` 명령을 변경하여 소프트웨어 버전을 자동으로 연결할 수 있습니다.
 
 Each `ENV` line creates a new intermediate layer, just like `RUN` commands. This means that even if you unset the environment variable in a future layer, it still persists in this layer and its value can be dumped. You can test this by creating a Dockerfile like the following, and then building it.

> 각각의 `ENV` 라인은 `RUN` 명령어와 같은 새로운 중간 레이어를 생성합니다. 이것은 여러분들이 미래의 레이어에 환경 변수를 설정하지 않음에도 불구하고 이 레이어에 영속할 것이고 값들이 덤플될수 있습니다. 여러분들은 다음과 같이 Dockerfile을 생성함으로써 테스트할 수 있고 빌드할 수 있습니다.

```dockerfile
# syntax=docker/dockerfile:1
FROM alpine
ENV ADMIN_USER="mark"
RUN echo $ADMIN_USER > ./mark
RUN unset ADMIN_USER
```

```console
$ docker run --rm test sh -c 'echo $ADMIN_USER'

mark
```

To prevent this, and really unset the environment variable, use a `RUN` command with shell commands, to set, use, and unset the variable all in a single layer. You can separate your commands with `;` or `&&`. If you use the second method, and one of the commands fails, the `docker build` also fails. This is usually a good idea. Using `\` as a line continuation character for Linux Dockerfiles improves readability. You could also put all of the commands into a shell script and have the `RUN` command just run that shell script.

> 이것을 예방하고 환경 변수를 정말로 설정 해제하려면 shell 명령어와 함께 `RUN` 명령어를 사용하여 변수를 단일 레이어에서 모두 설정, 사용 및 설정 해제합니다. 여러분들은 `;`이나 `&&` 명령어들을 사용하여 분리할 수 있습니다. 만약 여러분들이 두번째 방법을 사용한다면 명령어들 중 하나가 실패하면 `docker build` 또한 실패할 것입니다. 일반적으로 리눅스 DOckerfile 파일의 줄 연속 문자로 `\`을 사용하면 가독성이 향상됩니다. 쉘 스크립트에 모든 명령어들을 넣고 `RUN` 명령어를 실행하면 됩니다.

```dockerfile
# syntax=docker/dockerfile:1
FROM alpine
RUN export ADMIN_USER="mark" \
    && echo $ADMIN_USER > ./mark \
    && unset ADMIN_USER
CMD sh
```

```shell
$ docker run --rm test sh -c 'echo $ADMIN_USER'
```

For more information about `ENV`, see [Dockerfile reference for the ENV instruction](https://docs.docker.com/engine/reference/builder/#env).

> `ENV` 에 대한 더 많은 정보는 ENV 명령어에 대한 Dockerfile 참조를 확인해주세요

### ADD or COPY
`ADD` and `COPY` are functionally similar. `COPY` supports basic copying of files into the container, from the [build context](https://docs.docker.com/build/building/context/) or from a stage in a [multi-stage build](https://docs.docker.com/build/building/multi-stage/). `ADD` supports features for fetching files from remote HTTPS and Git URLs, and extracting tar files automatically when adding files from the build context.

> `ADD` 와 `COPY`는 기능적으로 비슷한 기능입니다. `COPY`는 파일들을 빌드 컨텍스트나 멀티 스테이지 빌드안에 스테이지에서 컨테이너로 복사하는 것을 지원합니다. `ADD`는 HTTPS와 Git URLs로부터 파일을 가져오는 것에 대한 기능들을 지원하고 빌드 컨텍스트로부터 파일들을 추가할 때 자동적으로 tar 파일들을 추출하는 것을 지원합니다.

You'll mostly want to use `COPY` for copying files from one stage to another in a multi-stage build. If you need to add files from the build context to the container temporarily to execute a `RUN` instruction, you can often substitute the `COPY` instruction with a bind mount instead. For example, to temporarily add a `requirements.txt` file for a `RUN pip install` instruction:

> 멀티 스테이지 빌드안의 한 단계에서 다른 단계로 복사하는데 주로 `COPY`를 사용하고자 할 것입니다. 만약 여러분들이 빌드 컨텍스트에서 컨테이너로 파일들을 임시적으로 추가할 필요가 있다면 `RUN` 명령어를 사용하세요. 여러분들은 종종 바인드 마운트를 가진 `COPY` 명령어를 대체할 수 있습니다. 예를 들어 `RUN pip install` 명령에 대한 `requirements.txt` 파일을 임시로 추가하려면 다음과 같이 하십시오.

```dockerfile
RUN --mount=type=bind,source=requirements.txt,target=/tmp/requirements.txt \
    pip install --requirement /tmp/requirements.txt
```

Bind mounts are more efficient than `COPY` for including files from the build context in the container. Note that bind-mounted files are only added temporarily for a single `RUN` instruction, and don't persist in the final image. If you need to include files from the build context in the final image, use `COPY`.

> 바인드 마운트들은 컨테이너 안에 빌드 컨텍스트로부터 파일들을 포함하기 위해서 `COPY` 를 사용하는 것보다 더욱 효율적입니다. 바인드 마운트된 파일들은 오직 임시적으로 추가된 것임을 명심하세요. 그리고 최종 이미지에서 영속적이지 않습니다. 만약 최종 이미지안에 빌드 컨텍스트로부터 파일들을 포함할 필요가 있다면 `COPY`를 사용하세요.

The `ADD` instruction is best for when you need to download a remote artifact as part of your build. `ADD` is better than manually adding files using something like `wget` and `tar`, because it ensures a more precise build cache. `ADD` also has built-in support for checksum validation of the remote resources, and a protocol for parsing branches, tags, and subdirectories from [Git URLs](https://docs.docker.com/engine/reference/commandline/image_build/#git-repositories).

> `ADD` 명령어는 빌드의 일부분으로써 여러분들이 원격 아티팩트를 다운로드하기 위해서 필요할 때 최고입니다. `ADD`는 `wget`과 `tar` 과 같은 것들을 사용하여 파일들을 손수 추가하는 것보다 더 났습니다. 왜냐하면 `ADD`는  보다 정확한 빌드 캐시를 보장하기 때문입니다. `ADD`는 또한 원격 리소스들의 체크섬 검증을 내장 지원합니다. 그리고 Git URL들로부터 브랜치를 추출하기 위한 프로토콜, 태그, 서브 디렉토리들에 대한 체크섬 검증도 지원합니다.

The following example uses `ADD` to download a .NET installer. Combined with multi-stage builds, only the .NET runtime remains in the final stage, no intermediate files.

> 다음 예제는 .NET 인스톨러를 다운로드하기 위해 `ADD`를 사용합니다. 멀티 스테이지 빌드와 결합하면, .NET 런타임만 최종 단계에 남아 중간 파일은 없습니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM scratch AS src
ARG DOTNET_VERSION=8.0.0-preview.6.23329.7
ADD --checksum=sha256:270d731bd08040c6a3228115de1f74b91cf441c584139ff8f8f6503447cebdbb \
    https://dotnetcli.azureedge.net/dotnet/Runtime/$DOTNET_VERSION/dotnet-runtime-$DOTNET_VERSION-linux-arm64.tar.gz /dotnet.tar.gz

FROM mcr.microsoft.com/dotnet/runtime-deps:8.0.0-preview.6-bookworm-slim-arm64v8 AS installer

# Retrieve .NET Runtime
RUN --mount=from=src,target=/src <<EOF
mkdir -p /dotnet
tar -oxzf /src/dotnet.tar.gz -C /dotnet
EOF

FROM mcr.microsoft.com/dotnet/runtime-deps:8.0.0-preview.6-bookworm-slim-arm64v8

COPY --from=installer /dotnet /usr/share/dotnet
RUN ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet
```

For more information about `ADD` or `COPY`, see the following:

- [Dockerfile reference for the ADD instruction](https://docs.docker.com/engine/reference/builder/#add)
- [Dockerfile reference for the COPY instruction](https://docs.docker.com/engine/reference/builder/#copy)

> `ADD`나 `COPY` 명령어에 대한 더욱 자세한 정보는 다음을 참고해주세요.

### ENTRYPOINT
The best use for `ENTRYPOINT` is to set the image's main command, allowing that image to be run as though it was that command, and then use `CMD` as the default flags.

> `ENTRYPOINT`는 이미지의 메인 명령어를 설정하여 이미지가 마치 명령어인 것처럼 실행되도록 한 다음 `CMD`를 기본 플래그로 사용하는 것이 가장 좋습니다.

The following is an example of an image for the command line tool `s3cmd`:

> 다음 예제는 `s3cmd` CLI에 대한 이미지의 예제입니다.

```dockerfile
ENTRYPOINT ["s3cmd"]
CMD ["--help"]
```

You can use the following command to run the image and show the command's help:

> 다음 명령어를 실행하여 이미지를 실행하고 명령어의 도움말을 출력합니다.

```shell
$ docker run s3cmd
```

Or, you can use the right parameters to execute a command, like in the following example:

> 또는 여러분들은 명령어를 실행시키기 위해서 오른쪽에 파라미터를 다음과 같이 사용할 수 있습니다.

```shell
$ docker run s3cmd ls s3://mybucket
```

This is useful because the image name can double as a reference to the binary as shown in the command above.

이미지 이름이 위의 명령과 같이 이진법의 참조로 두배가 될 수 있기 때문에 유용합니다.

The `ENTRYPOINT` instruction can also be used in combination with a helper script, allowing it to function in a similar way to the command above, even when starting the tool may require more than one step.

> `ENTRYPOINT` 명령어는 또한 툴이 한개 이상의 단계를 요구할 때에도 위 명령어와 비슷한 방법으로 동작하는 helper script와의 조합으로 사용될 수 있습니다.

For example, the [Postgres Official Image](https://hub.docker.com/_/postgres/) uses the following script as its `ENTRYPOINT`:

> 다음 예제는 Postgres 공식 이미지가 다음 스크립트를 `ENTRYPOINT`로 사용합니다.

```bash
#!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

This script uses [the `exec` Bash command](https://wiki.bash-hackers.org/commands/builtin/exec) so that the final running application becomes the container's PID 1. This allows the application to receive any Unix signals sent to the container. For more information, see the [`ENTRYPOINT` reference](https://docs.docker.com/engine/reference/builder/#entrypoint).

> 이 스크립트는 최종 실행중인 애플리케이션이 컨테이너의 PID 1이 될수 있도록 exec Bash 명령어를 사용합니다. 이를 통해 애플리케이션은 컨테이너로 전송되는 모든 유닉스 신호를 수신할 수 있습니다. 더 많은 정보는 `ENTRYPOINT` 참조를 참고해주세요

In the following example, a helper script is copied into the container and run via `ENTRYPOINT` on container start:

> 다음 예제는 컨테이너 시작시 `ENTRYPOINT`를 통해 실행하고 helper script가 컨테이너로 복사됩니다.

```dockerfile
COPY ./docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
```

This script lets you interact with Postgres in several ways.

> 이 스크립트는 여러분들에게 여러 방법으로 Postgres와 상호작용하게 됩니다.

It can simply start Postgres:

> 이것은 간단한게 Postgres를 시작할 수 있습니다.

```bash
$ docker run postgres
```

Or, you can use it to run Postgres and pass parameters to the server:

> 또는 여러분들은 서버에 파라미터를 전달하고 Postgres를 실행할 수 있습니다.

```bash
$ docker run postgres postgres --help
```

Lastly, you can use it to start a totally different tool, such as Bash:

> 마지막으로 여러분들은 Bash와 같은 방법 다른 도구로 사용할 수 있습니다.

```
$ docker run --rm -it postgres bash
```

For more information about `ENTRYPOINT`, see [Dockerfile reference for the ENTRYPOINT instruction](https://docs.docker.com/engine/reference/builder/#entrypoint).

> `ENTRYPOINT`에 대한 더 많은 정보는 ENTRYPOINT 명령어를 위한 Dockerfile 참조를 참고해주세요

### Volume
You should use the `VOLUME` instruction to expose any database storage area, configuration storage, or files and folders created by your Docker container. You are strongly encouraged to use `VOLUME` for any combination of mutable or user-serviceable parts of your image.

> 도커 컨테이너에 의해서 생성된 디렉토리들, 파일들, 설정 저장소, 데이터베이스 스토리지 영역을 내보내기 위해서 `VOLUME` 명령어를 사용하여야 합니다. 이미지의 변경 가능한 부분이나 사용자가 사용할 수 있는 부분의 조합에는 `VOLUME`을 사용하는것이 좋습니다.

For more information about `VOLUME`, see [Dockerfile reference for the VOLUME instruction](https://docs.docker.com/engine/reference/builder/#volume).

> `VOLUME`에 대한 더 많은 정보는 VOLUME 명령어에 대한 Dockerfile 참조를 확인해주세요

### USER
If a service can run without privileges, use `USER` to change to a non-root user. Start by creating the user and group in the Dockerfile with something like the following example:

> 만약 한 서비스가 권한 없이 동작 가능해야 한다면, `USER` 명령어를 사용하여 루트 유저가 아닌  사용자로 변경합니다. 다음 예제와 같이 Dockerfile에서 사용자와 그룹을 생성함해서 시작하세요.

```shell
$ RUN groupadd -r postgres && useradd --no-log-init -r -g postgres postgres
```

>**Note**
>Consider an explicit UID/GID.
>Users and groups in an image are assigned a non-deterministic UID/GID in that the "next" UID/GID is assigned regardless of image rebuilds. So, if it’s critical, you should assign an explicit UID/GID.

> **Note**
> 명시적인 UID/GID를 고라혀세요.
> 이미지에 있는 Users와 groups는 이미지 재빌드와 관계없이 "next" UID/GID가 할당된다는 점에서 비결정론적인 UID/GID가 할당됩니다. 그래서 만약 이것이 치명적인 문제라면, 여러분들은 명시적인 UID/GID를 할당하세요.

> **Note**
> Due to an [unresolved bug](https://github.com/golang/go/issues/13548) in the Go archive/tar package's handling of sparse files, attempting to create a user with a significantly large UID inside a Docker container can lead to disk exhaustion because `/var/log/faillog` in the container layer is filled with NULL (\0) characters. A workaround is to pass the `--no-log-init` flag to useradd. The Debian/Ubuntu `adduser` wrapper does not support this flag.

> **Note**
> Go archive/tar 패키지의 희소 파일 처리에서 unresolved bug로 인해서 도커 컨테이너 내부에서 상당히 큰 UID를 가진 사용자를 생성하려고 하면 컨테이너 계층의 `/var/log/failog`가 NULL(\0) 문자로 채워지기 때문에 디스크가 소진될 수 있습니다. 해결 방법은 useradd 명령어에 --no-log-init 플래그를 전달하는 것입니다. Debian/Ubuntu 계열 `adduser` 명령어는 이 옵션을 지원하지 않습니다.

Avoid installing or using `sudo` as it has unpredictable TTY and signal-forwarding behavior that can cause problems. If you absolutely need functionality similar to `sudo`, such as initializing the daemon as `root` but running it as non-`root`, consider using [“gosu”](https://github.com/tianon/gosu).

> `sudo`는 TTY(teletypewriter)를 예측할 수 없고 신호 전달 동작이 문제를 일으킬 수 있으므로 설치하거나 사용하지 마세요. 데몬을 루트로 초기화하되 비루트로 실행하는 등 `sudo`와 유사한 기능이 꼭 필요한 경우에는 `gosu`를 사용하는 것을 고려해보세요.

Lastly, to reduce layers and complexity, avoid switching `USER` back and forth frequently.

> 마지막으로 레이어와 복잡성을 줄이기 위해서 `USER`를 자주 앞뒤로 전환하지 마세요.

For more information about `USER`, see [Dockerfile reference for the USER instruction](https://docs.docker.com/engine/reference/builder/#user)
> `USER`에 대한 더 많은 정보는 USER 명령어에 대한 Dockerfile 참조를 확인해주세요

### WORKDIR
For clarity and reliability, you should always use absolute paths for your `WORKDIR`. Also, you should use `WORKDIR` instead of proliferating instructions like `RUN cd … && do-something`, which are hard to read, troubleshoot, and maintain.

> 명확성과 신뢰성을 위해서 항상 `WORKDIR`에 절대 경로를 사용해야 합니다. 또한 `RUN cd ... && do-something`과 같은 명령어 대신에 `WORKDIR`를 사용하세요. 

For more information about `WORKDIR`, see [Dockerfile reference for the WORKDIR instruction](https://docs.docker.com/engine/reference/builder/#workdir).

> `WORKDIR`에 대한 더 자세한 정보는 WORKDIR 명령어에 대한 Dockerfile 참조를 확인해주세요

### ONBUILD
An `ONBUILD` command executes after the current Dockerfile build completes. `ONBUILD` executes in any child image derived `FROM` the current image. Think of the `ONBUILD` command as an instruction that the parent Dockerfile gives to the child Dockerfile.

> `ONBUILD` 명령어는 현재 Dockerfile이 빌드를 완료한 후에 실행합니다. `ONBUILD`는 현재 이미지에서 파생된 어떤 자식 이미지에서든 실행되는 지시문입니다. `ONBUILD` 명령어는 부모 Dockerfile이 자식 Dockerfile에 제공하는 명령어라고 생각하세요.

A Docker build executes `ONBUILD` commands before any command in a child Dockerfile.

> Docker 빌드는 `ONBUILD` 명령어를 자식 Dockerfile안에 명령어 전에 실행합니다. 

`ONBUILD` is useful for images that are going to be built `FROM` a given image. For example, you would use `ONBUILD` for a language stack image that builds arbitrary user software written in that language within the Dockerfile, as you can see in [Ruby’s `ONBUILD` variants](https://github.com/docker-library/ruby/blob/c43fef8a60cea31eb9e7d960a076d633cb62ba8d/2.4/jessie/onbuild/Dockerfile).

> `ONBUILD`는 주어진 이미지에서 구축되는 이미지에 유용합니다. 예를 들어Ruby의 ONBUILD 변형에서 볼수 있듯이 해당 언어로 작성된 임의의 사용자 소프트웨어를 도커 파일 내에 빌드하는 언어 스택 이미지에 `ONBUILD`를 사용합니다.

Images built with `ONBUILD` should get a separate tag. For example, `ruby:1.9-onbuild` or `ruby:2.0-onbuild`.

> `ONBUILD`가 내장된 이미지는 별도의 태그를 얻어야 합니다. 예를 들어 `ruby:1.9-onbuild`나 `ruby:2.0-onbuild`가 있습니다.

Be careful when putting `ADD` or `COPY` in `ONBUILD`. The onbuild image fails catastrophically if the new build's context is missing the resource being added. Adding a separate tag, as recommended above, helps mitigate this by allowing the Dockerfile author to make a choice.

> `ONBUILD`에 `ADD`나 `COPY`를 넣을때 조심하세요. 만약 새로운 빌드의 컨텍스트가 추가되어야할 리소스를 빠뜨린다면 onbuild 이미지는 실패할 것입니다. 위와 같은 명령어와 같이 별도의 태그를 추가해서 Dockerfile 작성자가 선택하는 것을 허용하게 함으로써 이를 완화하는데 도움이 됩니다.

For more information about `ONBUILD`, see [Dockerfile reference for the ONBUILD instruction](https://docs.docker.com/engine/reference/builder/#onbuild).

> `ONBUILD`에 대한 더 많은 정보는 ONBUILD 명령어에 대한 Dockerfile을 참고해주세요








