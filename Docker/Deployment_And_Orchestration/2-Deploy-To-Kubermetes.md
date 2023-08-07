## Deploy to Kubernetes

### 선행조건(Prerequisites)

- Download and install Docker Desktop as described in [Get Docker](https://docs.docker.com/get-docker/).
- Work through containerizing an application in [Part 2](https://docs.docker.com/get-started/02_our_app/).
- Make sure that Kubernetes is enabled on your Docker Desktop:
    - **Mac**: Click the Docker icon in your menu bar, navigate to **Settings** and make sure there’s a green light
      beside ‘Kubernetes’.
    - **Windows**: Click the Docker icon in the system tray and navigate to **Settings** and make sure there’s a green
      light beside ‘Kubernetes’.

> - 도커 데스크톱을 다운로드하고 설치하세요.
> - Part2에서 애플리케이션을 컨테이너화하는 것을 작업하세요.
> - 도커 데스크톱에 쿠버네티스를 활성화하세요.
    > - Mac : Settings으로 들어가서 쿠버네티스를 활성하고 초록불을 확인하세요.
    > - Windows : 시스템 트레이에서 도커 아이콘을 클릭하고 Settings로 가세요. 그리고 쿠버네티스를 활성화하고 초록불을 확인하세요.

If Kubernetes isn’t running, follow the instructions
in [Orchestration](https://docs.docker.com/get-started/orchestration/) of this tutorial to finish setting it up.

> 만약 쿠버네티스가 실행되지 않는다면 설정을 끝내기 위해서 이 튜토리얼의 Orchestration 명령어를 따라하세요.

### 소개(Introduction)

Now that we’ve demonstrated that the individual components of our application run as stand-alone containers, it’s time
to arrange for them to be managed by an orchestrator like Kubernetes. Kubernetes provides many tools for scaling,
networking, securing and maintaining your containerized applications, above and beyond the abilities of containers
themselves.

> 이제 애플리케이션의 개별 구성 요소가 독립 실행형 컨테이너로 실행된다는 것을 입증했으므로, 쿠버네티스와 같은 오케스트라에 의해서 관리되는 컨테이너들을 준비할 시간입니다.
> 쿠버네티스는 컨테이너 자체의 능력 이상으로 컨테이너화된 애플리케이션의 스케일링, 네트워킹, 보안, 컨테이너 유지보수에 대한 많은 도구들을 제공합니다.

In order to validate that our containerized application works well on Kubernetes, we’ll use Docker Desktop’s built in
Kubernetes environment right on our development machine to deploy our application, before handing it off to run on a
full Kubernetes cluster in production. The Kubernetes environment created by Docker Desktop is fully featured, meaning
it has all the Kubernetes features your app will enjoy on a real cluster, accessible from the convenience of your
development machine.

> 컨테이너형 애플리케이션이 쿠버네티스에서 제대로 작동하는지 검증하기 위해 개발머신에서 Docker Desktop의 내장 쿠버네티스 환경을 사용하여 애플리케이션을 배포한 후 운영중인 전체 쿠버네티스 클러스터에서
> 실행되도록 제공합니다.도커 데스크톱에 의해서 생성된 쿠버네티스 환경은 완전한 기능을 갖추고 있습니다.
> 즉, 개발 머신의 편의성으로 접근할 수 있는 실제 클러스터에서 앱이 즐길 수 있는 모든 쿠버네티스 기능을 갖추고 있습니다.


