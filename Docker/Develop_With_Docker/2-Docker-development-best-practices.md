# Docker development best practices

The following development patterns have proven to be helpful for people building applications with Docker. If you have
discovered something we should add, let us know.

> 다음 개발 패터들은 도커를 가지고 애플리케일션을 빌드하는 사람들을 대상으로 도움되는 것을 증명받고 있습니다.
> 만약 여러분들이 추가해야할 무언가가 있다면 우리에게 알려주세요

## 이미지를 작게 유지하는 방법(How to keep your images small)

Small images are faster to pull over the network and faster to load into memory when starting containers or services.
There are a few rules of thumb to keep image size small:

> 작은 이미지는 컨테이너나 서비스가 시작할때 네트워크를 통해 pull 하는 속도가 더 빠르고 메모리에 로드하는 속도가 더 빠릅니다.
> 거기에는 이미지 사이즈를 작게 유지하는 몇가지 규칙이 있습니다.

- Start with an appropriate base image. For instance, if you need a JDK, consider basing your image on a Docker Official
  Image which includes OpenJDK, such as `eclipse-temurin`, rather than building your own image from scratch.

> - 적절한 베이스 이미지를 가지고 시작합니다. 예를 들어 JDK가 필요한 경우 이미지 처음부터 작성하는 대신 eclipse-temurin과 같은 OpenJD가 포함된 도커 공식 이미지를 기반으로 이미지를 작성하는
    것을 고려해보세요.

- Use multistage builds. For instance, you can use the maven image to build your Java application, then reset to the
  tomcat image and copy the Java artifacts into the correct location to deploy your app, all in the same Dockerfile.
  This means that your final image doesn’t include all of the libraries and dependencies pulled in by the build, but
  only the artifacts and the environment needed to run them.
    - If you need to use a version of Docker that does not include multistage builds, try to reduce the number of layers
      in your image by minimizing the number of separate RUN commands in your Dockerfile. You can do this by
      consolidating multiple commands into a single RUN line and using your shell’s mechanisms to combine them together.
      Consider the following two fragments. The first creates two layers in the image, while the second only creates
      one.

```shell
RUN apt-get -y update
RUN apt-get install -y python
```

```shell
RUN apt-get -y update && apt-get install -y python
```

> - 멀티 스테이지 빌드를 사용하세요. 예를 들어 메이븐 이미지를 사용하여 Java 애플리케이션을 구축한 다음 Tomcat 이미지로 재설정하고 Java 아티팩트를 올바른 위치에 복사하여 동일한 Docker 파일에
    앱을 배포할 수 있습니다. 따라서 최종 이미지에는 빌드에서 가져온 라이브러리와 종속성이 모두 포함되지 않고 이러한 라이브러리를 실행하는데필요한 아티팩트와 환경만 포함됩니다.
> - 만약 멀티 스테이지 빌드를 포함하지 않은 도커의 버전을 사용해야 한다면 Dockerfile에 RUN 명령어를 최소화하여 이미지의 레이어 개수를 줄이세요. 여러 명령어를 단일한 RUN 라인으로 통합하고
    shell의 메커니즘을 사용하여 이를 결합하여 이를 수행할 수 있습니다. 다음 두 조각들을 고려하세요. 첫번째는 이미지에 두개의 레이어를 생성하고 두번째 조각은 하나만 생성합니다.

```shell
RUN apt-get -y update
RUN apt-get install -y python
```

```shell
RUN apt-get -y update && apt-get install -y python
```

- If you have multiple images with a lot in common, consider creating your
  own [base image](https://docs.docker.com/build/building/base-images/) with the shared
  components, and basing your unique images on that. Docker only needs to load the common layers once, and they are
  cached. This means that your derivative images use memory on the Docker host more efficiently and load more quickly.
- To keep your production image lean but allow for debugging, consider using the production image as the base image for
  the debug image. Additional testing or debugging tooling can be added on top of the production image.
- When building images, always tag them with useful tags which codify version information, intended destination (`prod`
  or
  `test`, for instance), stability, or other information that is useful when deploying the application in different
  environments. Do not rely on the automatically-created `latest` tag.

> - 공통점이 많은 이미지가 여러 개 있는 경우 공유 구성요소를 사용하여 기본 이미지를 만들고 이를 기반으로 고유 이미지를 만드는 것을 고려해보세요. 도커는 오직 공통 레이어를 한번만 로드되면 캐시됩니다. 이는
    파생 이미지가 도커 호스트의 메모리를 더 효율적으로 사용하고 더 빨리 로한다는 것을 의미합니다.
> - 프로덕션 이미지를 유지하되 디버깅을 허용하려면 프로덕션 이미지를 디버그 이미지의 기본 이미지로 사용하는 것을 고려해보세요. 추가적인 테스팅 또는 디버깅 도구는 프로덕션 이미지의 최상단에 추가될 수 있습니다.
> - 이미지를 빌드할때 항상 버전 정보, 목적(예: 제품 또는 테스트), 안정성 또는 애플리케이션을 다른 환경에 배포할때 유용한 기타 정보를 코드화하는 유용한 태그를 사용하여 이미지에 태그를 작성합니다. 자동으로
    생성된 최신 태그를 신뢰하지 마세요.

## 애플리케이션 데이터를 영속하는 위치 및 방법(Where and how to persist application data)

- Avoid storing application data in your container’s writable layer
  using [storage drivers](https://docs.docker.com/storage/storagedriver/select-storage-driver/). This increases the size
  of your container and is less efficient from an I/O perspective than using volumes or bind mounts.

> 저장소 드라이버를 사용하여 여러분들 컨테이너의 작성 가능한 레이어에서 애플리케이션 데이터를 저장하는 것을 피하세요.
> 이렇게 하면 컨테이너의 크기가 증가되어 I/O 측면에서 볼륨 또는 바인드 만운드를 사용하는 것보다 효율성이 떨어집니다.

- Instead, store data using [volumes](https://docs.docker.com/storage/volumes/).

> 대신에 볼륨을 사용하여 데이터를 저장하세요.

- One case where it is appropriate to use [bind mounts](https://docs.docker.com/storage/bind-mounts/) is during
  development, when you may want to mount your source directory or a binary you just built into your container. For
  production, use a volume instead, mounting it into the same location as you mounted a bind mount during development.

> 바인드 마운트를 사용하는 것이 적절한 한 가지 경우는 개발 중에 소스 디렉토리나 컨테이너에 방금 구축한 바이너리를 마운트하는 경우입니다.
> 프로덕션의 경우 개발 중에 바인드 마운트 대신 볼륨을 대신 사용하여 바인드 마운트를 장착한 위치와 동일한 위치에 볼륨을 마운트합니다.

For production, use [secrets](https://docs.docker.com/engine/swarm/secrets/) to store sensitive application data used by
services, and use [configs](https://docs.docker.com/engine/swarm/configs/) for non-sensitive data such as configuration
files. If you currently use standalone containers, consider migrating to use single-replica services, so that you can
take advantage of these service-only features.

> 프로덕션의 경우 시크릿(secrets)을 사용하여 서비스에서 사용하는 중요한 애플리케이션 데이터를 저장하고 설정 파일과 같은 중요하지 않은 데이터에 대해 구성을 사용합니다.

## 테스트와 개발을 위한 CI/CD(Use CI/CD for testing and deployment)

- When you check in a change to source control or create a pull request, use Docker Hub or another CI/CD pipeline to
  automatically build and tag a Docker image and test it.

> 소스 제어 변경을 체크인하거나 풀 리퀘스트를 생성할때 도커 허브 또는 또다른 CI/CD 파이프라인을 빌드를 자동화하고 도커 이미지를 태그하고 테스트하는데 사용하세요.

- Take this even further by requiring your development, testing, and security teams
  to [sign images](https://docs.docker.com/engine/reference/commandline/trust/) before they are
  deployed into production. This way, before an image is deployed into production, it has been tested and signed off by,
  for instance, development, quality, and security teams.

> 개발팀, 테스팅팀 그리고 보안팀들에서 이미지를 프로덕션으로 배포하기 전에 이미지 서명하도록 요구해서 이를 더욱 개선할 수 있습니다.
> 이러한 방식으로, 이미지가 프로덕션에 배포되기 전에 이미지는 개발, 품질 및 보안 팀등에 의해서 테스트되고 승인됩니다.

## 개발과 프로덕션 환경의 차이(Differences in development and production environments)

### Development

- Use bind mounts to give your container access to your source code.
- Use Docker Desktop for Mac or Docker Desktop for Windows.
- Don’t worry about time drift.

> - 소스코드에 컨테이너 접근하기 위해서 바인드 마운트를 사용한다
> - 맥 또는 윈도우에서 도커 데스크톱을 사용한다
> - 시간 동기화에 대해서 걱정 없다

### Production

- Use volumes to store container data.
- Use Docker Engine, if possible with userns mapping for greater isolation of Docker processes from host processes.
- Always run an NTP client on the Docker host and within each container process and sync them all to the same NTP
  server. If you use swarm services, also ensure that each Docker node syncs its clocks to the same time source as the
  containers.

> - 컨테이너 데이터를 저장하기 위해서 볼륨들을 사용한다
> - 가능한 경우 사용자 매핑과 함께 도커 엔진을 사용하여 도커 프로세스를 호스트 프로세스에서 더 많이 분리할 수 있습니다.
> - 언제나 도커 호스트에서 NTP(Network Time Procotol) 클라이언트를 실행하고 각각의 컨테이너 프로세스 내에서 수행하고 같은 NTP 서버에 그들 모두를 동기화합니다.
> - 만약 여러분들이 스웜 서비스를 사용한다면 각각의 도커 노드가 해당 클럭을 컨테이너와 동일한 시간 소스에 동기화해야 합니다.






