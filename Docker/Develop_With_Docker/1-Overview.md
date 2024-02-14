# Develop with Docker

This page contains a list of resources for application developers who would like to build new applications using Docker.

> 이 페이지는 도커를 사용하여 새로운 애플리케이션을 빌드하고자 하는 애플리케이션 개발자들을 대상으로 한 리소스들의 목록을 포함하고 있습니다.

## 선행 조건(Prerequisites)

Work through the learning modules in [Get started](https://docs.docker.com/get-started/) to understand how to build an
image and run it as a containerized application.

> 이미지를 빌드하기 위한 방법을 이해하기 위해서 Get started에 있는 모듈들을 배우세요.
> 그리고 컨테이너화된 애플레킹션을 실행해보세요.

## 도커에 새로운 앱을 개발하기(Develop new apps on Docker)

If you’re just getting started developing a brand new app on Docker, check out these resources to understand some of the
most common patterns for getting the most benefits from Docker.

> 이제 막 도커에서 새로운 앱을 개발하기 시작했다면, 도커의 장점을 최대한 활용하기 위해서 가장 일반적인 패턴을 몇가지 이해하려면 이러한 리소스를 확인하세요.

- Learn how to [build an image](https://docs.docker.com/engine/reference/builder/) using a Dockerfile
- Use [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) to keep your images lean
- Manage application data using [volumes](https://docs.docker.com/storage/volumes/)
  and [bind mounts](https://docs.docker.com/storage/bind-mounts/)
- [Scale your app with Kubernetes](https://docs.docker.com/get-started/kube-deploy/)
- [Scale your app as a Swarm service](https://docs.docker.com/get-started/swarm-deploy/)
- [General application development best practices](https://docs.docker.com/develop/dev-best-practices/)

> - Dockerfile을 사용하여 이미지를 빌드하기 위한 방법을 배우기
> - 멀티 스테이지 빌드를 사용하여 이미지를 얇게 유지하기
> - 볼륨과 바인드 마운트를 사용하여 애플리케이션 데이터를 관리하기
> - 쿠버네티스를 가지고 앱을 확장하기
> - 스웜 서비스로서 앱을 확장하기
> - 일반적인 애플리케이션 개발 모범 사례

## 도커를 가지고 특정 언어로 명세한 앱 개발에 대하여 배우기(Learn about language-specific app development with Docker)

- [Docker for Java developers lab](https://github.com/docker/labs/tree/master/developer-tools/java/)
- [Port a node.js app to Docker lab](https://github.com/docker/labs/tree/master/developer-tools/nodejs/porting)
- [Ruby on Rails app on Docker lab](https://github.com/docker/labs/tree/master/developer-tools/ruby)
- [Dockerize a .Net Core application](https://docs.docker.com/language/dotnet/)
- [ASP.NET Core application with SQL Server using Docker Compose](https://github.com/docker/awesome-compose/tree/master/aspnet-mssql)

## SDK 또는 API를 가지고 고급 개발(Advanced development with the SDK or API)

After you can write Dockerfiles or Compose files and use Docker CLI, take it to the next level by using Docker Engine
SDK for Go/Python or use the HTTP API directly. Visit
the [Develop with Docker Engine API](https://docs.docker.com/engine/api/) section to learn more about
developing with the Engine API, where to find SDKs for your programming language of choice, and to see some examples.

> Dockerfile이나 Composefile을 작성하고 Docker CLI을 사용한 후 GO/Python용 Docker 엔진 SDK를 사용하거나 HTTP API를 직접 사용합니다.
> Docker 엔진 API을 사용하여 개발하는 방법과 원하는 프로그래밍 언어의 SDK를 찾을 수 있는 방법 및 몇가지 예를 보려면 Development with Docker Engine API 섹션을 방문하십시오.


