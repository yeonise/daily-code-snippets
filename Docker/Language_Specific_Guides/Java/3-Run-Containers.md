## Run your image as a container

### 선행 조건(Prerequisites)

Work through the steps to build a Java image
in [Build your Java image](https://docs.docker.com/language/java/build-images/).

> 자바 이미지를 빌드하기 위해서 이 단계를 통하여 작업합니다.

### 개요(Overview)

In the previous module, we created our sample application and then we created a Dockerfile that we
used to build an image. We created our image using the command `docker build`. Now that we have an
image, we can run that image and see if our application is running correctly.

> 이전 장에서는 우리는 샘플 애플리케이션을 생성하고나서 이미지를 빌드하기 위해서 사용되는 Dockerfile을 작성하였습니다.
> 우리는 `docker build` 명령어를 사용하여 이미지를 생성하였습니다.
> 지금 우리는 이미지를 가지고 있습니다. 우리는 이미지를 실행하여 애플리케이션이 정확하게 수행되는지 볼 수 있습니다.

A container is a normal operating system process except that this process is isolated and has its
own file system, its own networking, and its own isolated process tree separated from the host.

> 컨테이너는 일반적인 운영체제 프로세스입니다. 단, 이 프로세스는 분리되어 있으며 자체 파일 시스템, 자체 네트워킹 및 호스트에서 분리된 자체 프로세스 트리를 가지고 있습니다.

To run an image inside a container, we use the `docker run` command. The `docker run` command
requires
one parameter which is the name of the image. Let’s start our image and make sure it is running
correctly. Run the following command in your terminal:

> 컨테이너 내부에서 이미지를 실행하려면 `docker run` 명령을 사용합니다.
> `docker run` 명령어는 하나의 파라미터를 요구합니다. 파라미터는 이미지의 이름입니다.
> 우리의 이미지를 시작하고 실행되는지 확인합니다.
> 터미널에서 다음 명령어를 실행하세요.

```shell
$ docker run java-docker
```

After running this command, you’ll notice that we did not return to the command prompt. This is
because our application is a REST server and runs in a loop waiting for incoming requests without
returning control back to the OS until we stop the container.

> 명령어 실행후에 명령어 프롬프트에 어떤 응답도 받지 않은 것을 볼것입니다.
> 왜냐하면 우리의 애플리케이션이 REST 서버이고 컨테이너를 멈출때까지 OS 제어권을 다시 돌려주지 않고 들어오는 요청을 기다리는 루프에서 실행되기 때문입니다.

Let’s open a new terminal then make a GET request to the server using the curl command.

> 새로운 터미널을 열고 `curl` 명령어를 사용하여 서버에 GET 요청을 날려봅니다.

```shell
$ curl --request GET \
--url http://localhost:8080/actuator/health \
--header 'content-type: application/json'
curl: (7) Failed to connect to localhost port 8080: Connection refused
```

As you can see, our `curl` command failed because the connection to our server was refused. It means
that we were not able to connect to the localhost on port 8080. This is expected because our
container is running in isolation which includes networking. Let’s stop the container and restart
with port 8080 published on our local network.

> 여러분들이 보는대로 우리의 curl 명령어는 실패했습니다. 이유는 서버와의 연결이 거부됬기 때문입니다.
> 이것은 우리가 localhost:8080에 연결이 불가능하다는 것을 의미합니다.
> 우리의 컨테이너가 네트워크를 포함한 격리된 환경에서 실행중이기 때문이라고 예상됩니다.
> 컨테이너를 중지하고 로컬 네트워크에 8080 포트를 발행하여 다시 시작해봅시다.

To stop the container, press ctrl-c. This will return you to the terminal prompt.

> 컨테이너를 종료하기 위해서 Ctrl-c를 누르세요. 이 종료는 터미널 프롬프트로 돌아갈 것입니다.

To publish a port for our container, we’ll use the `--publish` flag (`-p` for short) on
the `docker run`
command. The format of the `--publish` command is `[host port]:[container port]`. So, if we wanted
to
expose port 8000 inside the container to port 8080 outside the container, we would pass `8080:8000`
to
the `--publish` flag.

> 컨테이너에 포트를 발행하기 위해서는 `--publish`(줄여서 -p) 옵션을 `docker run` 명령어 위에서 사용할 것입니다.
> `--publish` 명령어 형식은 `[host port]:[container port]`입니다.
> 따라서 컨테이너 내부의 포트 8000을 컨테이너 외부의 포트 8080에 노출시키려면 8080:8000을 --publish 옵션에 설정합니다.

Start the container and expose port 8080 to port 8080 on the host.

> 컨테이너를 시작하고 호스트의 포트 8080에 포트 8080을 표시합니다.

```shell
$ docker run --publish 8080:8080 java-docker
```

Now, let’s rerun the curl command from above.

> 지금 위의 curl 명령어를 재실행해봅시다.

```shell
$ curl --request GET \
--url http://localhost:8080/actuator/health \
--header 'content-type: application/json'
{"status":"UP"}
```

Success! We were able to connect to the application running inside of our container on port 8080.

Now, press ctrl-c to stop the container.

> 성공! 우리는 8080 포트로 우리의 실행중인 컨테이너 안쪽의 애플리케이션에 연결할 수 있었습니다.
> 지금 ctrl-c를 눌러 컨테이너를 중지하세요.

### 분리 모드에서 실행하기(Run in detached mode)

This is great so far, but our sample application is a web server and we don’t have to be connected
to the container. Docker can run your container in detached mode or in the background. To do this,
we can use the `--detach` or `-d` for short. Docker starts your container as earlier, but this time,
it will “detach” from the container and return you to the terminal prompt.

> 지금까지 좋았습니다. 그러나 우리의 샘플 애플리케이션은 웹 서버입니다. 그리고 우리는 컨테이너에 연결될 필요가 없습니다.
> 도커는 우리의 컨테이너를 분리된 모드 또는 백그라운드로 실행할 수 있습니다.
> 그러기 위해서는 우리는 `--detach` 또는 `-d`를 줄여서 사용할 수 있습니다.
> 도커는 여러분들의 컨테이너를 쉽게 시작합니다. 그러나 도커는 이전과 같이 컨테이너를 시작하지만, 이번에는 컨테이너에서 "분리"되어 터미널 프롬프트로 돌아갑니다.

```shell
$ docker run -d -p 8080:8080 java-docker
5ff83001608c7b787dbe3885277af018aaac738864d42c4fdf5547369f6ac752
```

Docker started our container in the background and printed the Container ID on the terminal.

> 도커는 우리의 컨테이너를 백그라운드로 시작합니다. 그리고 나서 컨테이너 ID를 터미널에 출력합니다.

Again, let’s make sure that our container is running properly. Run the same curl command from above.

> 다시, 우리의 컨테이너가 제대로 돌아가는지 확인합니다. 위에서 동일한 curl 명령어를 실행합니다.

```ignore
$ curl --request GET \
--url http://localhost:8080/actuator/health \
--header 'content-type: application/json'
{"status":"UP"}
```

### 컨테이너 목록(List containers)

As we ran our container in the background, how do we know if our container is running, or what other
containers are running on our machine? Well, we can run the `docker ps` command. Just like how we
run
the `ps` command in Linux to see a list of processes on our machine, we can run the `docker ps`
command
to view a list of containers running on our machine.

> 우리는 백그라운드로 컨테이너를 실행했었기 때문에 우리는 만약 컨테이너가 실행중이면 어떻게 할것인지 또는 다른 컨테이너들은 우리의 머신에서 무엇을 실행중인지 어떻게 알것인가?
> 우리는 `docker ps` 명령어를 실행할 수 있습니다.
> 리눅스에서 ps 명령을 실행하여 컴퓨터의 프로세스 목록을 확인하는 것처럼 `docker ps` 명령어를 실행할 수 있습니다. 명령어를 실행하면 머신에서 실행 중인 컨테이너의
> 목록을 보여줍니다.

```ignore
$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS              PORTS                    NAMES
5ff83001608c   java-docker      "./mvnw spring-boot:…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp   trusting_beaver
```

The `docker ps` command provides a bunch of information about our running containers. We can see the
container ID, the image running inside the container, the command that was used to start the
container, when it was created, the status, ports that exposed and the name of the container.

> `docker ps` 명령어는 실행중인 컨테이너에 대한 정보의 묶음을 제공합니다.
> 우리는 컨테이너 ID, 컨테이너에서 실행중인 이미지, 컨테이너를 생성할때 컨테이너를 시작하는데 사용하는 명령어, 상태, 컨테이너의 이름과 노출되는 포트를 볼 수 있습니다.

You are probably wondering where the name of our container is coming from. Since we didn’t provide a
name for the container when we started it, Docker generated a random name. We’ll fix this in a
minute, but first we need to stop the container. To stop the container, run the `docker stop`
command
which does just that, stops the container. We need to pass the name of the container or we can use
the container ID.

> 여러분들은 아마 우리 컨테이너의 이름이 어디서 왔는지 궁금할 것입니다.
> 우리가 컨테이너를 시작할때 컨테이너에 대한 이름을 제공하지 않았기 때문에 도커는 랜덤한 이름을 생성합니다.
> 우리는 잠시후에 고치겠지만, 먼저 컨테이너를 중지해야 합니다. 컨테이너를 중지하기 위해서 `docker stop` 명령어를 실행하세요. 그렇게 하면 컨테이너가 중지됩니다.
> 우리는 컨테이너에 이름을 전달하는것이 필요합니다. 또는 우리는 컨테이너 ID를 사용할 수 있습니다.

```shell
$ docker stop trusting_beaver
trusting_beaver
```

Now, rerun the docker ps command to see a list of running containers.

> 지금, 실행중인 컨테이너들의 목록을 보기 위해서 `docker ps` 명령어를 재시작합니다.

```shell
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### 컨테이너 중지, 시작, 이름 지정하기(Stop, start, and name containers)

You can start, stop, and restart Docker containers. When we stop a container, it is not removed, but
the status is changed to stopped and the process inside the container is stopped. When we ran the
`docker ps` command in the previous module, the default output only shows running containers. When
we
pass the `--all` or `-a` for short, we see all containers on our machine, irrespective of their
start or
stop status.

> 여러분들은 도커 컨테이너들을 시작하고 중지하고 재시작할 수 있습니다. 컨테이너를 중지할 때, 삭제되지는 않습니다.
> 그러나 컨테이너 상태가 정지상태로 변경되면 컨테이너 안에 프로세스는 정지됩니다.
> 이전 장에서 `docker ps` 명령어를 실행할때 기본 추력은 오직 실행중인 컨테이너들을 보여주는 것이었습니다.
> 우리가 docker ps 명령어에 --all 또는 -a를 전달하면 우리는 모든 컨테이너들을 볼 수 있습니다. 모든 컨테이너들은 시작 또는 정지 상태를 포함합니다.

```shell
$ docker ps -a
CONTAINER ID   IMAGE               COMMAND                  CREATED          STATUS                        PORTS                    NAMES
5ff83001608c   java-docker         "./mvnw spring-boot:…"   5 minutes ago    Exited (143) 18 seconds ago                            trusting_beaver
630f2872ddf5   java-docker         "./mvnw spring-boot:…"   11 minutes ago   Exited (1) 8 minutes ago                               modest_khayyam
a28f9d587d95   java-docker         "./mvnw spring-boot:…"   17 minutes ago   Exited (1) 11 minutes ago                              lucid_greider
```

You should now see several containers listed. These are containers that we started and stopped, but
have not been removed.

> 우리는 지금 여러개의 컨테이너 목록들을 볼수 있습니다. 이것들은 우리가 시작하고 정지한 컨테이너들입니다.
> 그러나 삭제되지는 않았습니다.

Let’s restart the container that we just stopped. Locate the name of the container we just stopped
and replace the name of the container below using the `restart` command.

> 우리가 정지한 컨테이너를 재시작해봅시다. 우리가 정지한 컨테이너의 이름을 위치시킵니다. 그리고
> restart 명령어를 사용하여 아래 컨테이너의 이름을 대체합니다.

```shell
$ docker restart trusting_beaver
```

Now, list all the containers again using the docker ps command.

> 지금 모든 컨테이너들을 다시한번 `docker ps` 명령어를 사용하여 목록화하세요.

```shell
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                      PORTS                    NAMES
5ff83001608c   java-docker   "./mvnw spring-boot:…"   10 minutes ago   Up 2 seconds                0.0.0.0:8080->8080/tcp   trusting_beaver
630f2872ddf5   java-docker   "./mvnw spring-boot:…"   16 minutes ago   Exited (1) 13 minutes ago                            modest_khayyam
a28f9d587d95   java-docker   "./mvnw spring-boot:…"   22 minutes ago   Exited (1) 16 minutes ago                            lucid_greider
```

Notice that the container we just restarted has been started in detached mode and has port 8080
exposed. Also, observe the status of the container is “Up X seconds”. When you restart a container,
it starts with the same flags or commands that it was originally started with.

> 우리가 재시작한 컨테이너는 분리된 모드에서 시작하였고 8080 노출된 포트를 가집니다.
> 또한 컨테이너의 상태가 "Up X seconds"인지 확인합니다.
> 컨테이너를 재시작할때 컨테이너는 원래 시작한것과 동일한 옵션 또는 명령어로 시작합니다.

Now, let’s stop and remove all of our containers and take a look at fixing the random naming issue.
Find the name of your running container and replace the name in the command below with the name of
the container on your system.

> 지금 우리의 컨테이너들 전부를 중지하고 제거해봅시다. 그리고나서 랜덤한 네이밍 문제를 해결해 보겠습니다.
> 여러분들의 실행중인 컨테이너를 탐색하세요. 그리고 여러분들의 시스템 위에 컨테이너의 이름을 가지고 아래 명령어에 이름을 대체하세요.

```shell
$ docker stop trusting_beaver
trusting_beaver
```

Now that our container is stopped, let’s remove it. When you remove a container, the process inside
the container will be stopped and the metadata for the container will be removed.

> 지금 우리의 컨테이너가 정지되었습니다. 컨테이너를 삭제해봅시다. 여러분들의 컨테이너를 삭제할때 컨테이너 안에
> 프로세스는 정지하고 컨테이너에 대한 메타 데이터는 삭제될 것입니다.

To remove a container, simply run the `docker rm` command passing the container name. You can pass
multiple container names to the command using a single command. Again, replace the container names
in the following command with the container names from your system.

> 컨테이너를 삭제하기 위해서 간단하게 `docker rm` 명령어를 실행하세요.
> 여러분들은 여러개의 컨테이너 이름들을 단일 명령어를 사용하여 명령어에 전달할 수 있습니다.
> 다시, 컨테이너 다음 명령어에 여러분들의 시스템으로부터의 컨테이너 이름들을 대체하세요.

```shell
$ docker rm trusting_beaver modest_khayyam lucid_greider
trusting_beaver
modest_khayyam
lucid_greider
```

Run the docker ps --all command again to see that all containers are removed.

> 모든 컨테이너들이 삭제되었는지 보기 위해서 다시한번 `docker ps --all` 명령어를 실행하세요.

Now, let’s address the random naming issue. The standard practice is to name your containers for the
simple reason that it is easier to identify what is running in the container and what application or
service it is associated with.

> 지금, 랜덤 네이밍 문제를 살펴보겠습니다. 일반적인 방법은 컨테이너 이름을 지정하는 것입니다.
> 컨테인어ㅔ서 실행중인 것과 컨테이너가 연결된 애플리케이션 또는 서비스를 더쉽게 식별할 수 있다는 단순한 이유입니다.

To name a container, we just need to pass the `--name` flag to the `docker run` command.

> 컨테이너 이름을 지정하기 위해서는 우리는 `docker run` 명령어에 --name 옵션을 전달할 필요가 있습니다.

```shell
$ docker run --rm -d -p 8080:8080 --name springboot-server java-docker
2e907c68d1c98be37d2b2c2ac6b16f353c85b3757e549254de68746a94a8a8d3

$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                    NAMES
2e907c68d1c9   java-docker   "./mvnw spring-boot:…"   8 seconds ago   Up 8 seconds   0.0.0.0:8080->8080/tcp   springboot-server
```

That’s better! We can now easily identify our container based on the name.

> 훨씬 낫네요! 우리는 지금 이름을 기반으로 쉽게 우리의 컨테이너를 식별할 수 있습니다.

### 다음 단계(Next steps)

In this module, we took a look at running containers, publishing ports, and running containers in
detached mode. We also took a look at managing containers by starting, stopping, and, restarting
them. We also looked at naming our containers so they are more easily identifiable.

> 이번장에서는 우리는 실행중인 컨테이너, 포트 발행하기, 백그라운드 모드에서 컨테이너 실행하기를 살펴봤습니다.
> 컨테이너들이 시작, 중지, 재시작함으로써 관리해볼 수 있었습니다.
> 우리는 또한 컨테이너 이름을 지정해봤습니다.

In the next module, we’ll learn how to run a database in a container and connect it to our
application. See:

> 다음 장에서는 우리는 컨테인어ㅔ서 데이터베이스를 실행하는 방법에 대해서 배우고 우리의 애플리케이션에 데이터베이스를 연결하는 방법에 대해서 배울 것입니다.


