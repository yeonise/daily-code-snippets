## What next

Although we’re done with our get started guide, there’s still a LOT more to learn about containers!
We’re not going to go deep-dive here, but here are a few other areas to look at next!

> 시작 가이드는 끝났지만 거기에는 여전히 컨테이너에 대해서 배울것이 많습니다.
> 우리는 여기서 깊숙이 배우지는 않을 것입니다. 그러나 여기 다음에 살펴볼 몇가지 다른 영역이 있습니다.

### 컨테이너 오케스트레이션(Container orchestration)

Running containers in production is tough. You don’t want to log into a machine and simply run a
`docker run` or `docker-compose up`. Why not? Well, what happens if the containers die? How do you
scale
across several machines? Container orchestration solves this problem. Tools like Kubernetes, Swarm,
Nomad, and ECS all help solve this problem, all in slightly different ways.

> 프로덕션 환경에서 컨테이너들을 실행하는것은 어렵습니다. 여러분들은 컴퓨터로 직접 실행하지 않고 간단하게 `docker run` 이나
> `docker-compose up` 명령어를 실행하면 됩니다.
> 만약 컨테이너들이 죽는다면 어떻게 될까요? 어떻게 여러개의 머신들에 걸쳐서 확장할까요? 컨테이너 오케스트레이션은 이 문제를 해결합니다.
> Kubernetes, Swarm, Nomad, ECS와 같은 도구들은 전부 이 문제를 해결하는데 도와줄 수 있습니다. 물론 미세하게 방법들이 얀간씩 다릅니다.

The general idea is that you have “managers” who receive expected state. This state might be “I want
to run two instances of my web app and expose port 80.” The managers then look at all of the
machines in the cluster and delegate work to “worker” nodes. The managers watch for changes (such as
a container quitting) and then work to make actual state reflect the expected state.

> 일반적인 아이디어는 예상되는 상태를 받는 "매니저"가 있습니다.
> 이 예상되는 상태는 “I want to run two instances of my web app and expose port 80.”와 같을수 있습니다.
> 그런 다음 매니저는 클러스터의 모든 시스템을 살펴보고 "worker" 노드에 작업을 위임합니다.
> 매니저는 변경 사항(예: 컨테이너 종료)을 확인한 다음에 실제 상탱를 예상되는 상태로 반영합니다.

### 클라우드 네이티브 컴퓨팅 기반 프로젝트(Cloud Native Computing Foundation projects, CNFF projects)

The CNCF is a vendor-neutral home for various open-source projects, including Kubernetes,
Prometheus, Envoy, Linkerd, NATS, and more! You can view
the [graduated and incubated projects](https://www.cncf.io/projects/) here
and the entire [CNCF Landscape](https://www.cncf.io/projects/) here. There are a LOT of projects to
help solve problems around monitoring, logging, security, image registries, messaging, and more!

> CNCF는 다양한 오픈소스 프로젝트를 위한 벤더-네추럴 홈입니다.
> CNCF에는 Kubernetes, Prometheus, Envoy, Linkerd, NATS 등이 포함됩니다.
> 여기에서 등급별 및 인큐베이터 프로젝트를 볼 수 있습니다.
> 거기에는 모니터링, 로깅, 시큐리티, 이미지 레지스트리, 메시징 등 도움을 바라는 많은 프로젝트들이 있습니다.

So, if you’re new to the container landscape and cloud-native application development, welcome!
Please connect with the community, ask questions, and keep learning! We’re excited to have you!

> 컨테이너 환경과 클라우드 네이티브 애플리케이션 개발에 익숙하지 않은 분들은 환영합니다.
> 커뮤니티에 질문하고 계속해서 배우세요! 우리는 당신이 있어 신납니다.!

### 비디오 워크샵으로 시작하기(Getting started video workshop)

We recommend the video workshop from DockerCon 2022. Watch the video below or use the links to open
the video at a particular section.

> DockercCon 2022 비디오 워크샵을 추천합니다.
> 아래 비디오를 보거나 특정 섹션에 비디어를 열기 위해 링크를 사용해주세요.

- [Docker overview and installation](https://www.youtube.com/watch?v=gAGEar5HQoU&feature=youtu.be)
- [Pull, run, and explore containers](https://www.youtube.com/watch?t=1400&v=gAGEar5HQoU&feature=youtu.be)
- [Build a container image](https://www.youtube.com/watch?t=3185&v=gAGEar5HQoU&feature=youtu.be)
- [Containerize an app](https://www.youtube.com/watch?t=4683&v=gAGEar5HQoU&feature=youtu.be)
- [Connect a DB and set up a bind mount](https://www.youtube.com/watch?v=gAGEar5HQoU&t=6305s)
- [Deploy a container to the cloud](https://www.youtube.com/watch?t=8280&v=gAGEar5HQoU&feature=youtu.be)

### 스크래치로부터 컨테이너 생성하기(Creating a container from scratch)

If you’d like to see how containers are built from scratch, Liz Rice from Aqua Security has a
fantastic talk in which she creates a container from scratch in Go. While the talk does not go into
networking, using images for the filesystem, and other advanced topics, it gives a deep dive into
how things are working.

> 만약 여러분들이 컨테이너 스크래치로부터 어떻게 빌드되는지 보고 싶다면, Aqua Security의 Liz Rice가 Go에서 처음부터 컨테이너를 만드는 강연을 합니다.
> 이 강연은 네트워킹, 파일 시스템용 이미지 사용 및 기타 고급 주제에 대해서는 다루지 않지만, 어떻게 작동하는지에 대해서 설명합니다.

[링크](https://www.youtube.com/watch?v=8fi7uSYlOdc)

### 언어 명세 가이드(Language-specific guides)

If you are looking for information on how to containerize an application using your favorite
language, see [Language-specific getting started guides.](https://docs.docker.com/language/)

> 만약 여러분들이 좋아하는 언어를 사용하여 애플리케이션을 커스터마이즈 하는 방법을 보고 싶다면 언어 명세 가이드를 봐주세요.






