## Deploy your app

Now that we have configured a CI/CD pipeline, let’s look at how we can deploy the application. Docker supports deploying
containers on Azure ACI and AWS ECS. You can also deploy your application to Kubernetes if you have enabled Kubernetes
in Docker Desktop.

> 지금 우리는 CI/CD 파이프라인을 설정하였습니다.
> 우리가 어떻게 애플리케이션을 배포하는지 알아봅니다.
> 도커는 Azure ACI와 AWS ECS에서 컨테이너에들에 배포하는 것을 지원합니다.
> 또한 도커 데스크톱에서 쿠버네티스(Kubernetes)를 사용하도록 설정한 경우 쿠버네티스에 애플리케이션을 배포할 수 있습니다.

### Docker and Azure ACI

The Docker Azure Integration enables developers to use native Docker commands to run applications in Azure Container
Instances (ACI) when building cloud-native applications. The new experience provides a tight integration between Docker
Desktop and Microsoft Azure allowing developers to quickly run applications using the Docker CLI or VS Code extension to
switch seamlessly from local development to cloud deployment.

> Docker Azure 통합은 개발자들에게 클라우드-네이티브 애플리케이션을 빌드할때
> Azure 컨테이너 인스턴스에서 애플리케이션을 실행하기 위해서 네티이브 도커 명령어를 사용하는 것을 활성화합니다.
> 새로운 경험을 통해 개발자는 도커 데스크톱과 마이크로소프트 Azure간의 긴밀한 통합을 통해 도커 CLI 또는 VS Code 확장을 사용하여 애플리케이션을 신속하게 실행하여 로컬 개발에서 클라우드 배포로 원할하게
> 전환할 수 있습니다.

For detailed instructions, see [Deploying Docker containers on Azure.](https://docs.docker.com/cloud/aci-integration/)

> 자세한 명령어들은 여기를 Azure에서 도커 컨테이너에 배포하기를 참고해주세요.

### Docker and AWS ECS

The Docker ECS Integration enables developers to use native Docker commands in the Docker Compose CLI to run
applications in an Amazon EC2 Container Service (ECS) when building cloud-native applications.

> 개발자는 Docker ECS 통합을 사용하여 클라우드 네이티브 애플리케이션을 구축할 때 Docker Compose CLI의 네이티브 도커 명령어를 사용하여
> ECS(Amazon EC2 Container Service)에서 애플레케이션을 실행할 수 있습니다.

The integration between Docker and Amazon ECS allows developers to use the Docker Compose CLI to set up an AWS context
in one Docker command, allowing them to switch from a local context to a cloud context and run applications quickly and
easily to simplify multi-container application development on Amazon ECS using Compose files.

> 도커와 Amazon EC2 사이에 통합은 개발자들에게 단일 도커 명령어로 AWS 컨텍스트를 설정하는 Docker Compose CLI를 사용하는 것을 허용합니다.
> 단일 도커 명령어로 로컬 컨텍스트에서 클라우드 컨텍스트로 전환하고 애플리케이션을 신속하게 실행할 수 있도록 지원하는 명령어입니다.
> 컴포즈 파일을 사용하여 EC2에서 다중 컨테이너 애플리케이션 개발을 쉽게 단순화할 수 있습니다.

For detailed instructions, see [Deploying Docker containers on ECS.](https://docs.docker.com/cloud/ecs-integration/)

> 자세한 명령어들은 ECS에서 도커 컨테이너에 배포하기를 참고해주세요.

### Kubernetes

Docker Desktop includes a standalone Kubernetes server and client, as well as Docker CLI integration that runs on your
machine. When you enable Kubernetes, you can test your workloads on Kubernetes.

> 도커 데스크톱은 표준 쿠버네티스 서버와 클라이언트를 포함합니다. 쿠버네티스 뿐만 아니라 여러분들의 머신에서 실행하는 도커 CLI 통합도 포함됩니다.
> 쿠버네티스를 활성화할때 여러분들은 쿠버네티스에서 여러분들의 워크로드를 테스트할 수 있습니다.

To enable Kubernetes:

1. From the Docker menu, select Settings.
2. Select Kubernetes and click Enable Kubernetes.

This starts a Kubernetes single-node cluster when Docker Desktop starts.

> 쿠버네티스를 활성화하기 위해서는 다음과 같습니다.
>
> 1. 도커 메뉴로부터 설정을 선택하세요.
> 2. 쿠버네티스를 선택하고 활성화를 클릭하세요.
>
> 이것은 도커 데스크톱 시작시 쿠버네티스 싱글 노드 클러스터를 시작합니다.

For detailed information, see [Deploy on Kubernetes](https://docs.docker.com/desktop/kubernetes/)
and [Describing apps using Kubernetes YAML](https://docs.docker.com/get-started/kube-deploy/#describing-apps-using-kubernetes-yaml)
.

> 더 자세한 정보는 쿠버네티스에 배포하기와 쿠버네티스 YAML을 사용한 앱 설명하기를 참고해주세요.

