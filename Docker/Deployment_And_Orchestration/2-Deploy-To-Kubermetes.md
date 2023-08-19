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

### 쿠버네티스 YAML을 사용한 앱 설명하기(Describing apps using Kubernetes YAML)

All containers in Kubernetes are scheduled as pods, which are groups of co-located containers that share some resources.
Furthermore, in a realistic application we almost never create individual pods; instead, most of our workloads are
scheduled as deployments, which are scalable groups of pods maintained automatically by Kubernetes. Lastly, all
Kubernetes objects can and should be described in manifests called Kubernetes YAML files. These YAML files describe all
the components and configurations of your Kubernetes app, and can be used to easily create and destroy your app in any
Kubernetes environment.

> 쿠버네티스 안에 모든 컨테이너들은 일부 리소스들을 공유하는 동일한 위치에 있는 컨테이너 그룹인 포드(pods)로서 스케줄 되어 있습니다.
> 또한 현실적인 애플리케이션에서는 거의 개별 포드를 생성하지 않습니다. 대신 대부분의 워크로드가 쿠버네티스에 의해서 자동으로 유지되는 확장 가능한 포드 그룹인 배포로 예약됩니다.
> 마지막으로 모든 쿠버네티스 객체는 쿠버네티스 YAML 파일이라는 manifest에서 설명될 수 있고 설명되어야 합니다.
> 이 YAML 파일들은 쿠버네티스 앱의 모든 컴포넌트와 설정을 설명합니다. 그리고 어떤 쿠버네티스 환경에서 여러분드르이 앱을 쉽게 생성하고 제거하는데 사용됩니다.

1. You already wrote a very basic Kubernetes YAML file in the Orchestration overview part of this tutorial. Now, let’s
   write a slightly more sophisticated YAML file to run and manage our Todo app, the container getting-started image
   created in Part 2 of the Quickstart tutorial. Place the following in a file called bb.yaml:

> 1. 여러분들은 이미 오케스트레이션 개요 파트 부분에서 매우 간단한 쿠버네티스 YAML 파일을 작성한적이 있습니다.
> 2. 이제는 조금더 복잡한 YAML 파일을 작성하여 Todo 앱을 실행하고 관리해보겠습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bb-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      bb: web
  template:
    metadata:
      labels:
        bb: web
    spec:
      containers:
        - name: bb-site
          image: getting-started
          imagePullPolicy: Never
---
apiVersion: v1
kind: Service
metadata:
  name: bb-entrypoint
  namespace: default
spec:
  type: NodePort
  selector:
    bb: web
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30001
```

In this Kubernetes YAML file, we have two objects, separated by the ---:

> 쿠버네티스 YAML 파일에서 우리는 `"---"`으로 구분된 두개의 객체들을 가지고 있습니다.

- A `Deployment`, describing a scalable group of identical pods. In this case, you’ll get just one `replica`, or copy of
  your pod, and that pod (which is described under the `template`: key) has just one container in it, based off of your
  `getting-started` image from the previous step in this tutorial.
- A `NodePort` service, which will route traffic from port 30001 on your host to port 3000 inside the pods it routes to,
  allowing you to reach your Todo app from the network.

> - 동일한 포드(pods)의 확장이 가능한 그룹을 설명하는 `Deployment`입니다. 이 경우 포드의 복제본 또는 복사본이 하나만 주어지고, 해당 포드(`template`: key 아래에 설명됨)에는 이
    튜토리얼의 이전 단계에서 가져온 `getting-started` 이미지를 기반으로 한 컨테이너가 하나만 들어있습니다.
> - 호스트의 포트 30001에서 전송되는 포트 내부의 포트 3000으로 트래픽을 라우팅하여 네트워크에서 ToDo 앱에 도달할 수 있는 `NodePort` 서비스입니다.

Also, notice that while Kubernetes YAML can appear long and complicated at first, it almost always follows the same
pattern:

> 또한 쿠버네티스 YAML 파일이 처음에는 길고 복잡해보이지만 거의 항상 같은 패턴을 따릅니다.

- The `apiVersion`, which indicates the Kubernetes API that parses this object
- The `kind` indicating what sort of object this is
- Some `metadata` applying things like names to your objects
- The `spec` specifying all the parameters and configurations of your object.

> 객체를 추출하는 쿠버네티스 API를 나타내는 `apiVersion`
> 어떤 종류의 객체인지를 나타내는 `kind`
> 여러분들의 객체들에 이름과 같은 것들을 적용하는 `metadata`
> `spec`은 여러분들의 객체의 모든 파라미터들과 설정들을 적용합니다.

### 여러분들의 애플리케이션을 배포하고 확인하기(Deploy and check your application)

1. In a terminal, navigate to where you created bb.yaml and deploy your application to Kubernetes:

> 1. 터미널에서 bb.ayml 파일로 이동하고 쿠버네티스에 애플리케이션을 배포하세요.

```shell
$ kubectl apply -f bb.yaml
```

you should see output that looks like the following, indicating your Kubernetes objects were created successfully:

> 여러분들은 다음과 같은 결과를 볼것입니다. 여러분들의 쿠버네티스 객체들이 성공적으로 생성되었다는 것을 볼 것입니다.

```shell
deployment.apps/bb-demo created
service/bb-entrypoint created
```

2. Make sure everything worked by listing your deployments:

> 2. 배포 목록을 확인하여 모든 작업이 잘 작동하는지 확인합니다.

```shell
$ kubectl get deployments
```

if all is well, your deployment should be listed as follows:

> 만약 모든것이 잘되면, 여러분들의 배포는 다음과 같이 목록화될것입니다.

```shell
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
bb-demo   1/1     1            1           40s
```

This indicates all one of the pods you asked for in your YAML are up and running. Do the same check for your services:

> 이것은 YAML에서 요청한 모든 포드가 작동 중임을 나타냅니다. 서비스에 대해서 동일한 확인을 하세요.

```shell
$ kubectl get services

NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
bb-entrypoint   NodePort    10.106.145.116   <none>        3000:30001/TCP   53s
kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP          138d
```

In addition to the default `kubernetes` service, we see our `bb-entrypoint` service, accepting traffic on port
30001/TCP.

> 기본적인 쿠버네티스 서비스에 우리의 bb-entrypoint 서비스가 보입니다. 이 서비스는 30001/TCP 트래픽을 받고 있습니다.

3. Open a browser and visit your Todo app at localhost:30001; you should see your Todo application, the same as when we
   ran it as a stand-alone container in Part 2 of the Quickstart tutorial.

> 3. 브라우저를 열고 localhost:30001로 접속하세요. 여러분들의 Todo 애플리케이션을 볼 수 있을 것입니다. 이 결과는 퀵스타트 튜토리얼에 Part 2에서 표준 컨테이너로서 컨테이너를 실행할때와
     동일합니다.

4. Once satisfied, tear down your application:

> 애플리케이션을 다운시키세요.

```shell
$ kubectl delete -f bb.yaml
```

### 결론(Conclusion)

At this point, we have successfully used Docker Desktop to deploy our application to a fully-featured Kubernetes
environment on our development machine. We haven’t done much with Kubernetes yet, but the door is now open; you can
begin adding other components to your app and taking advantage of all the features and power of Kubernetes, right on
your own machine.

> 이 시점에서 우리는 여러분들의 개발 머신에서 완벽하게 동작하는 쿠버네티스 환경에 우리의 애플리케이션을 배포하기 위해서 도커 데스크톱을 성공적으로 사용하였습니다.
> 우리는 쿠버네티스를 아직 많이 배우지는 않았습니다. 그러나 문은 지금 열려 있습니다. 앱에 다른 컴포넌트 요소를 추가하고 자신의 컴퓨터에서 바로 쿠버네티스의 모든 기능과 기능을 활용할 수 있습니다.

In addition to deploying to Kubernetes, we have also described our application as a Kubernetes YAML file. This simple
text file contains everything we need to create our application in a running state. We can check it into version control
and share it with our colleagues, allowing us to distribute our applications to other clusters (like the testing and
production clusters that probably come after our development environments) easily.

> 쿠버네티스에 배포하는것뿐만 아니라 우리는 쿠버네티스 YAML 파일에 애플리케이션을 설명하였습니다.
> 이 간단한 텍스트 파일은 실행 상태에서 애플리케이션을 생성하기 위해 필요한 모든 것을 포함하고 있습니다.
> 우리는 버전 제어로 확인하여 동료와 공유할 수 있으므로 애플리케이션을 다른 클러스(예: 개발 환경 다음에 나올 테스트 및 운영 클러스터)에 쉽게 배포할 수 있습니다.

### 쿠버네티스 참고(Kubernetes references)

Further documentation for all new Kubernetes objects used in this article are available here:

> 모든 새로운 쿠버네티스 객체에 대한 더욱 많은 문서들은 여기 있습니다.

- [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/)
- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/)




