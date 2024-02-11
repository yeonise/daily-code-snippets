# Overview of best practices for writing Dockerfiles
This topic covers recommended best practices and methods for building efficient images. It provides [general guidelines for your Dockerfiles](https://docs.docker.com/develop/develop-images/guidelines/) and more [specific best practices for each Dockerfile instruction](https://docs.docker.com/develop/develop-images/instructions/).

> 이 주제는 효율적인 이미지들을 빌드하기 위한 방법과 권장사항을 다룹니다. 이 권장사항은 여러분들의 Dockerfile들에 대해서 일반적인 가이드라인을 제공하고 각각의 Dockerfile 명령어에 대해서 더 특정한 권장사항을 제공합니다.

## What is a Dockerfile?
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

## Best practices for Dockerfile instructions

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

