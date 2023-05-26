## Overview

### 개요(Overview)

Welcome! We’re excited that you want to learn Docker.

This guide contains step-by-step instructions on how to get started with Docker.
Some of the things you’ll learn and do in this guide are:

- Build and run an image as a container.
- Share images using Docker Hub.
- Deploy Docker applications using multiple containers with a database.
- Run applications using Docker Compose.

> 어서오세요! 우리는 여러분들이 도커(Docker)를 배우기 원하는 것을 환영합니다.
> 이 가이드는 도커를 시작하는 방법에 대한 설명들을 단계별로 포함하고 있습니다.
> 이 가이드에서 배우고 수행할 작업은 다음과 같습니다.
> - 이미지를 컨테이너로서 빌드시키고 수행시키기
> - 도커 허브(Docker Hub)를 사용하여 이미지들을 공유하기
> - 데이터베이스가 포함된 여러 개의 컨테이너들을 사용하여 도커 애플리케이션들을 배포해보기
> - 도커 컴포즈(Docker Compose)를 사용하여 애플리케이션들을 실행시켜보기

Before you get to the hands on part of the guide, you should learn about containers and images.

> 여러분들이 가이드를 직접 해보기 전에, 여러분들은 컨테이너(container)와 이미지(image)들에 대해서 배워야 합니다.

### 컨테이너란 무엇인가?(What is a container?)

Simply put, a container is a sandboxed process on your machine that is isolated from
all other processes on the host machine. That isolation leverages kernel namespaces and cgroups,
features that have been in Linux for a long time. Docker has worked to make these capabilities
approachable and easy to use. To summarize, a container:

- Is a runnable instance of an image. You can create, start, stop, move, or delete a container
  using the DockerAPI or CLI.
- Can be run on local machines, virtual machines or deployed to the cloud.
- Is portable (can be run on any OS).
- Is isolated from other containers and runs its own software, binaries, and configurations.

> 간단히 말자하면 컨테이너는 호스트 머신 위에서 모든 다른 프로세스들과 격리된 시스템의 **샌드박스 프로세스**입니다.
> 이러한 격리 기술은 리눅스에서 오랫동안 사용되어 온 기능인 커널 네임스페이스(kernel namespaces)와 cgroup 기능을 활용합니다.
> 도커는 이러한 커널 네임스페이스와 cgroup과 같은 기능들을 접근하기 쉽고, 사용하기 쉽게 도와주는 기술입니다.
> 컨테이너를 요약하자면 다음과 같습니다.
> - 컨테이너는 이미지의 실행 가능한 인스턴스입니다. 여러분들은 도커API 또는 명령어 인터페이스(CLI)를 사용하여 컨테이너를
    > 생성하고, 시작하고, 정지하고, 이동시키거나 삭제할 수 있습니다.
> - 로컬 머신, 가상 머신 또는 배포된 클라우드 위에서 컨테이너를 실행시킬수 있습니다.
> - 컨테이너를 어떤 OS 환경에서든 실행시킬 수 있도록 휴대할 수 있습니다.
> - 컨테이너를 다른 컨테이너로부터 격리되어 자체적인 소프트웨어, 이진 및 설정들을 실행시킬 수 있습니다.

### 컨테이너 이미지란 무엇인가?(What is a container image?)

When running a container, it uses an isolated filesystem.
This custom filesystem is provided by a container image.
Since the image contains the container’s filesystem,
it must contain everything needed to run an application - all dependencies, configurations,
scripts, binaries, etc. The image also contains other configuration for the container,
such as environment variables, a default command to run, and other metadata.

> 컨테이너를 실행할 때 격리된 파일 시스템을 사용합니다.
> 이 커스텀한 파일 시스템은 컨테이너 이미지에 의해서 제공됩니다.
> 이미지는 컨테이너의 파일 시스템을 포함하기 때문에, 모든 의존성, 설정, 스크립트, 이진 등의 애플리케이션을 실행시키기
> 위해 필요한 모든 것을 포함해야 합니다.
> 그 이미지 또한 컨테이너를 위한 환경 변수, 실행을 위한 기본적인 명령어, 다른 메타데이터와 같은 설정을 포함하고 있습니다.

You’ll dive deeper into images later on in this guide, covering topics such as layering,
best practices, and more.

> 이 가이드는 후반부에서 계층화, 모범 사례 등과 같은 주제를 다루면서 이미지에 대해서 자세히 설명할 것입니다.

### 다음 단계(Next Steps)

In this section, you learned about containers and images.
In the next section, you’ll containerize your first application.

> 이 장에서는, 여러분들은 컨테이너와 이미지에 대해서 배웠습니다.
> 다음 장에서는, 여러분들은 여러분들의 첫번째 애플리케이션을 컨테이너화 할것입니다.
