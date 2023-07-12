## Deployment and orchestration

Containerization provides an opportunity to move and scale applications to clouds and data centers. Containers
effectively guarantee that those applications run the same way anywhere, allowing us to quickly and easily take
advantage of all these environments. Additionally, as we scale our applications up, we need some tooling to help
automate the maintenance of those applications, enable the replacement of failed containers automatically, and manage
the roll-out of updates and reconfigurations of those containers during their lifecycle.

> 컨테이너화는 애플리케이션을 클라우드 및 데이터 센터들로 이동하고 확장할 수 있는 기회를 제공합니다.
> 컨테이너들은 애플리케이션이 어디서나 동일한 방식으로 실행하도록 효과적으로 보장하므로 이러한 모든 환경을 빠르고 쉽게 활용할 수 있습니다.
> 추가적으로 애플리케이션을 확장함에 따라 이러한 애플리케이션의 유지보수를 자동화하고, 장애가 발생한 컨테이너를 자동으로 교체하며,
> 생명 주기 동안 해당 컨테이너의 롤 아웃 및 재구성을 관리하는데 도움이 되는 도구가 필요합니다.
> 롤아웃은 애플리케이션 또는 서비스의 새로운 버전을 점진적으로 배포하는 프로세스를 의미합니다.

Tools to manage, scale, and maintain containerized applications are called orchestrators. Two of the most popular
orchestration tools are Kubernetes and Docker Swarm. Docker Desktop provides development environments for both of these
orchestrators.

> 컨테이너화된 애플리케이션들을 관리, 확장, 유지보수하는 도구를 오케스트라(orchestrators)라고 부릅니다.
> 가장 인기있는 오케스트라 도구 2개는 쿠버네티스(Kubernetes)와 도커 스웜(Docker Swarm)이 있습니다.
> 도커 데스크톱은 오케스트라의 두가지에 대하여 개발 환경을 제공합니다.

The advanced modules teach you how to:

> 고급 모듈을 통해 다음 작업을 수행할 수 있습니다.

1. [Set up and use a Kubernetes environment on your development machine](https://docs.docker.com/get-started/kube-deploy/)
2. [Set up and use a Swarm environment on your development machine](https://docs.docker.com/get-started/swarm-deploy/)

### 쿠버네티스 활성화하기(Enable Kubernetes)

Docker Desktop will set up Kubernetes for you quickly and easily. Follow the setup and validation instructions
appropriate for your operating system:

> 도커 데스크톱은 빠르고 쉽게 쿠버네티스를 활성화 할 수 있습니다.
> 운영체제에 적합한 설정 및 유효성 검사 지침을 따릅니다.

#### Mac

1. After installing Docker Desktop, you should see a Docker icon in your menu bar. Click on it, and navigate to
   Settings > Kubernetes.

> 도커 데스크톱을 설치한후, 메뉴바에 도커 아이콘을 볼것입니다. 클리하여 Settings > Kubernets를 클릭하세요.

2. Check the checkbox labeled Enable Kubernetes, and click Apply & Restart. Docker Desktop will automatically set up
   Kubernetes for you. You’ll know that Kubernetes has been successfully enabled when you see a green light beside
   ‘Kubernetes running’ in Settings.

> 쿠버네티스를 활성화하는 체크박스 레이블을 선택하세요. 그리고 Apply & Restart를 클릭하세요.
> 도커 데스크톱은 자동으로 쿠버네티스를 준비할 것입니다.
> 여러분들은 설정에서 'Kubernets running' 옆에 초록불이 뜰때 쿠버네티스가 성공적으로 활성화된것을 알게 될 것입니다.

3. In order to confirm that Kubernetes is up and running, create a text file called pod.yaml with the following content:

> 쿠버네티스가 실행되는 것을 확인하기 위해서 다음 내용을 가진 pod.yaml 파일을 생성하세요.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo
spec:
  containers:
  - name: testpod
    image: alpine:latest
    command: ["ping", "8.8.8.8"]
```

This describes a pod with a single container, isolating a simple ping to 8.8.8.8.

> 파일은 싱글 컨테이너를 가진 pod를 설명합니다. 격리된 환경에서 8.8.8.8 ping 명령어를 실행합니다.

4. in a terminal, navigate to where you created pod.yaml and create your pod:

> 터미널에서 pod.yaml 파일이 생성된 곳으로 이동합니다. 그리고 pod를 생성합니다.

```shell
$ kubectl apply -f pod.yaml
```

5. Check that your pod is up and running:

> pod가 실행중인지 확인합니다.

```shell
$ kubectl get pods
```

You should see something like:

> 여러분들은 다음과 같은 결과를 볼 것입니다.

```shell
NAME      READY     STATUS    RESTARTS   AGE
demo      1/1       Running   0          4s
```

6. Check that you get the logs you’d expect for a ping process:

> ping 명령어 진행과정에 대한 로그를 확인하세요.

```shell
$ kubectl logs demo
```

You should see the output of a healthy ping process:

> ping 프로세스 진행과정의 출력을 볼 것입니다.

```shell
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=37 time=21.393 ms
64 bytes from 8.8.8.8: seq=1 ttl=37 time=15.320 ms
64 bytes from 8.8.8.8: seq=2 ttl=37 time=11.111 ms
```

7. Finally, tear down your test pod:

> 마지막으로 pod를 내리세요.

```shell
$ kubectl delete -f pod.yaml
```

#### Windows

1. After installing Docker Desktop, you should see a Docker icon in your system tray. Right-click on it, and navigate **
   Settings > Kubernetes**.

> 도커 데스크톱 설치후에 여러분들의 시스템 트레이에서 도커 아이콘을 볼것입니다. 도커 아이콘을 우클릭하여 Settings > Kubernets로 이동하세요.

2. Check the checkbox labeled **Enable Kubernetes**, and click **Apply & Restart**. Docker Desktop will automatically
   set up Kubernetes for you. You’ll know that Kubernetes has been successfully enabled when you see a green light
   beside ‘Kubernetes running’ in the **Settings** menu.

> 쿠버네티스를 활성화하는 체크박스 레이블을 체크하세요. 그리고 **Apply & Restart**를 클릭하세요.
> 도커 데스크톱은 자동적으로 쿠버네티스를 준비할 것입니다. 여러분들은 Settings 메뉴에서 'Kubernets running' 오른쪽에 초록불이 들어올때 성공적으로 활성화된것을 알 수 있을 것입니다.

3. In order to confirm that Kubernetes is up and running, create a text file called pod.yaml with the following content:

> 쿠버네티스가 실행되고 있는지 확인하기 위해서 다음 내용을 가진 pod.yaml 파일을 생성하세요.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo
spec:
  containers:
  - name: testpod
    image: alpine:latest
    command: ["ping", "8.8.8.8"]
```

This describes a pod with a single container, isolating a simple ping to 8.8.8.8.

> 단일 컨테이너를 가진 pod를 설명하고 있습니다. pod는 간단한 ping 8.8.8.8를 격리된 환경에서 수행합니다.

4. In PowerShell, navigate to where you created pod.yaml and create your pod:

> PowerShell에서 pod.yaml 파일 위치로 이동하고 pod를 생성하세요.

```javascript
$ kubectl apply -f pod.yaml
```

5. Check that your pod is up and running:

> pod가 실행하는 것을 확인하세요.

```javascript
$ kubectl get pods
```

You should see something like:

> 여러분들은 다음과 같은 결과를 볼 것입니다.

```shell
NAME      READY     STATUS    RESTARTS   AGE
demo      1/1       Running   0          4s
```

6. Check that you get the logs you’d expect for a ping process:

> ping 프로세스에 대한 로그를 확인하세요.

```shell
$ kubectl logs demo
```

You should see the output of a healthy ping process:

> ping 프로세스 헬스체크의 결과는 다음과 같을 것입니다.

```shell
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=37 time=21.393 ms
64 bytes from 8.8.8.8: seq=1 ttl=37 time=15.320 ms
64 bytes from 8.8.8.8: seq=2 ttl=37 time=11.111 ms
```

7. Finally, tear down your test pod:

> 마지막으로 pod를 내리세요.

```shell
$ kubectl delete -f pod.yaml
```

### 도커 Swarm 활성화하기(Enable Docker Swarm)

Docker Desktop runs primarily on Docker Engine, which has everything you need to run a Swarm built in. Follow the setup
and validation instructions appropriate for your operating system:

> 도커 데스크톱은 주로 Swarm을 실행하는데 필요한 모든것을 내장한 도커 엔진에서 실행됩니다.
> 운영체제에 따라 적절한 명령어들과 단계를 따라가세요.

#### Mac

1. Open a terminal, and initialize Docker Swarm mode:

> 터미널을 열고 Docker Swarm 모드를 초기화하세요.

```shell
$ docker swarm init
```

If all goes well, you should see a message similar to the following:

> 만약 잘되면 여러분들은 다음과 같은 메시지를 받을 것입니다.

```shell
Swarm initialized: current node (tjjggogqpnpj2phbfbz8jd5oq) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3e0hh0jd5t4yjg209f4g5qpowbsczfahv2dea9a1ay2l8787cf-2h4ly330d0j917ocvzw30j5x9 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

2. Run a simple Docker service that uses an alpine-based filesystem, and isolates a ping to 8.8.8.8:

> alpine 기반 파일시스템을 사용하는 간단한 도커 서비스를 실행하세요. 그리고 ping 8.8.8.8을 실행하세요.

```shell
$ docker service create --name demo alpine:latest ping 8.8.8.8
```

3. Check that your service created one running container:

> 생성하고 실행중인 컨테이너를 확인하세요.

```shell
$ docker service ps demo
```

You should see something like:

> 다음과 같은 결과를 볼것입니다.

```shell
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
463j2s3y4b5o        demo.1              alpine:latest       docker-desktop      Running             Running 8 seconds ago
```

4. Check that you get the logs you’d expect for a ping process:

> ping 프로세스에 대한 로그를 확인하세요.

```shell
$ docker service logs demo
```

You should see the output of a healthy ping process:

> 여러분들은 다음과 같은 ping 프로세스의 결과를 볼것입니다.

```shell
demo.1.463j2s3y4b5o@docker-desktop    | PING 8.8.8.8 (8.8.8.8): 56 data bytes
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=0 ttl=37 time=13.005 ms
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=1 ttl=37 time=13.847 ms
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=2 ttl=37 time=41.296 ms
```

5. Finally, tear down your test service:

> 마지막으로 테스트 서비스를 내리세요.

```shell
$ docker service rm demo
```

#### Windows

1. Open a powershell, and initialize Docker Swarm mode:

> powershell을 열고 Docker Swarm 모드를 초기화하세요.

```shell
$ docker swarm init
```

If all goes well, you should see a message similar to the following:

> 만약 모든게 잘되면 여러분들은 다음과 같은 비슷한 메시지를 받을 것입니다.

```shell
Swarm initialized: current node (tjjggogqpnpj2phbfbz8jd5oq) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3e0hh0jd5t4yjg209f4g5qpowbsczfahv2dea9a1ay2l8787cf-2h4ly330d0j917ocvzw30j5x9 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

2. Run a simple Docker service that uses an alpine-based filesystem, and isolates a ping to 8.8.8.8:

> alpine 기반 파일시스템을 사용하는 도커 서비스를 실행하세요. 그리고 ping 8.8.8.8을 격리된 환경에서 실행하세요.

```javascript
$ docker service create --name demo alpine:latest ping 8.8.8.8
```

3. Check that your service created one running container:

> 실행중인 컨테이너를 확인하세요.

```javascript
$ docker service ps demo
```

You should see something like:

> 여러분들은 다음과 같은 결과를 볼것입니다.

```shell
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
463j2s3y4b5o        demo.1              alpine:latest       docker-desktop      Running             Running 8 seconds ago
```

4. Check that you get the logs you’d expect for a ping process:

> ping 프로세스에 대한 로그를 확인하세요.

```javascript
$ docker service logs demo
```

You should see the output of a healthy ping process:

> 여러분들은 ping 프로세스의 결과를 볼것입니다.

```shell
demo.1.463j2s3y4b5o@docker-desktop    | PING 8.8.8.8 (8.8.8.8): 56 data bytes
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=0 ttl=37 time=13.005 ms
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=1 ttl=37 time=13.847 ms
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=2 ttl=37 time=41.296 ms
```

5. Finally, tear down your test service:

> 마지막으로 test 서비스를 내리세요.

```shell
$ docker service rm demo
```

### 결론(Conclusion)

At this point, you’ve confirmed that you can run simple containerized workloads in Kubernetes and Swarm. The next step
will be to write a YAML file that describes how to run and manage these containers.

> 이 시점에서 여러분들은 쿠버네티스와 스웜에서 간단한 컨테이너화된 워크로드를 실행하는 방법에 대해서 확인하였습니다.
> 다음 단계는 이러한 컨테이너들을 관리하고 실행하는 방법을 정의하는 YAML 파일을 작성할 것입니다.

- [Deploy to Kubernetes](https://docs.docker.com/get-started/kube-deploy/)
- [Deploy to Swarm](https://docs.docker.com/get-started/swarm-deploy/)

### CLI 참조 CLI(references)

Further documentation for all CLI commands used in this article are available here:

> 이번장에서 사용한 모든 CLI 명령어들에 대한 문서는 여기에서 이용가능합니다.

- [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply)
- [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get)
- [kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)
- [kubectl delete](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete)
- [docker swarm init](https://docs.docker.com/engine/reference/commandline/swarm_init/)
- [docker service *](https://docs.docker.com/engine/reference/commandline/service/)

