## Run your image as a container

### 선행조건(Prerequisites)

Work through the steps to build a Node JS image
in [Build your Node image](https://docs.docker.com/language/nodejs/build-images/).

> 노드 이미지 빌드하기 모듈에서 Node JS 이미지를 빌드하기 위한 단계를 수행해야 합니다.

### 개요(Overview)

In the previous module we created our sample application and then we created a Dockerfile that we used to create an
image. We created our image using the command `docker build`. Now that we have an image, we can run that image and see
if
our application is running correctly.

> 이전 장에서 우리는 간단한 애플리케이션을 생성하였고 이미지를 생성하기 위해서 사용되는 Dockerfile을 생성하였습니다.
> 우리는 `docker build` 명령어를 사용하여 우리의 이미지를 생성하였습니다.
> 이제 이미지가 생겼으니, 그 이미지를 실행하고, 그 이미지가 애플리케이션을 제대로 동작시키는지 확인해볼 수 있습니다.

A container is a normal operating system process except that this process is isolated and has its own file system, its
own networking, and its own isolated process tree separate from the host.

> 컨테이너는 일반적인 운영체제 프로세스입니다. 이 프로세스는 격리되었고 자기만의 파일 시스템, 네트워킹, 격리된 프로세스 트리를 가지고 있다는 것을 제외하면 일반적인 운영체제 프로세스입니다.

To run an image inside of a container, we use the `docker run` command. The `docker run` command requires one parameter
and
that is the image name. Let’s start our image and make sure it is running correctly. Execute the following command in
your terminal.

> 컨테이너 안에서 이미지를 실행시키기 위해서 우리는 `docker run` 명령어를 사용합니다.
> `docker run` 명령어는 하나의 파라미터인 이미지 이름을 요구합니다.
> 우리의 이미지를 시작하고 제대로 동작하는지 확인해봅시다.
> 여러분들의 터미널에 다음과 같은 명령어를 실행해주세요.

```shell
$ docker run node-docker
```

When you run this command, you’ll notice that you were not returned to the command prompt. This is because our
application is a REST server and will run in a loop waiting for incoming requests without returning control back to the
OS until we stop the container.

> `docker run` 명령어를 실행할때 여러분들은 프롬프트로부터 어떤 반응도 받지 않을 것입니다.
> 왜냐하면 여러분들의 애플리케이션은 REST server이고 컨테이너가 중지될때까지 OS에게 반환 제어 없이 들어오는 요청들에 대하여 대기중이기 때문입니다.

Let’s open a new terminal then make a POST request to the server using the curl command.

> 새로운 터미널을 열고 curl 명령어를 사용하여 서버에게 POST 요청을 날려봅시다.

```shell
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing"}'
curl: (7) Failed to connect to localhost port 8000: Connection refused
```

Our curl command failed because the connection to our server was refused. It means that we were not able to connect to
localhost on port 8000. This is expected because our container is running in isolation which includes networking. Let’s
stop the container and restart with port 8000 published on our local network.

> curl 명령어는 실패하였습니다. 왜냐하면 서버와의 연결이 거부되었기 때문입니다.
> 이는 포트 8000번 localhost로 연결이 불가능하다는 것을 의미합니다.
> 문제는 우리의 컨테이너가 네트워크를 포함한 격리된 공간에서 실행중이기 때문이라고 예상합니다.
> 컨테이너를 멈추고 우리의 로컬 네트워크에서 발행한 포트 8000번으로 재시작해봅시다.

To stop the container, press ctrl-c. This will return you to the terminal prompt.

> 컨테이너를 멈추기 위해서 Ctrl-C를 누르세요. 터미널 프롬프트 제어권은 다시 여러분들에게 돌아올 것입니다.

To publish a port for our container, we’ll use the `--publish` flag (`-p` for short) on the docker run command. The
format
of the `--publish` command is `[host port]:[container port]`. So if we wanted to expose port 8000 inside the container
to
port 3000 outside the container, we would pass 3000:8000 to the --publish flag.

> 컨테이너에 포트를 발행하기 위해서 우리는 `--publish` 옵션(줄여서 `-p`)을 docker run 명령어에서 사용할 것입니다.
> `--publish` 명령어의 형식은 `[host port]:[container port]`입니다. 그래서 만약 우리가 컨테이너 안에서 포트 8000번을 외부로 3000번으로 노출시키고 싶다면,
> 우리는 3000:8000을 --publish 옵션 값으로 전달하면 됩니다.

Start the container and expose port 8000 to port 8000 on the host.

> 컨테이너를 시작하고 호스트의 포트 8000에 포트 8000을 표시합니다.

```shell
$ docker run --publish 8000:8000 node-docker
```

Now let’s rerun the curl command from above. Remember to open a new terminal.

> 지금 위의 curl 명령어를 다시 실행봅시다. 새로운 터미널을 여는 것을 기억하세요.

```shell
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing"}'
{"code":"success","payload":[{"msg":"testing","id":"dc0e2c2b-793d-433c-8645-b3a553ea26de","createDate":"2020-09-01T17:36:09.897Z"}]}
```

Success! We were able to connect to the application running inside of our container on port 8000. Switch back to the
terminal where your container is running and you should see the POST request logged to the console.

> 성공입니다. 우리는 8000번 포트로 우리의 컨테이너안에서 실행중인 애플리케이션과 연결이 가능해졌습니다.
> 우리의 컨테이너가 실행중인 터미널에 돌아와서 POST 요청에 대한 로그가 찍힌 것을 확인해보세요.

```shell
2020-09-01T17:36:09:8770 INFO: POST /test
```

Press ctrl-c to stop the container.

> CTRL-C를 눌러 컨테이너를 중지하세요.

### 분리 모드에서 실행하기(Run in detached mode)

This is great so far, but our sample application is a web server and we should not have to have our terminal connected
to the container. Docker can run your container in detached mode or in the background. To do this, we can use the
`--detach` or `-d` for short. Docker will start your container the same as before but this time will “detach” from the
container and return you to the terminal prompt.

> 아직까지는 휼륭하지만, 애플리케이션은 웹 서버이므로 터미널을 컨테이너에 연결하지 않아도 됩니다.
> 도커는 여러분들의 컨테이너를 분리 모드 또는 백그라운드에서 실행할 수 있습니다.
> 그러기 위해서는 우리는 `--detach` 또는 줄여서 `-d`를 사용할 수 있습니다.
> 도커는 여러분들의 컨테이너를 이전과 동일하게 실행하지만 이번에는 컨테이너에서 "분리"되어 터미널 프롬프트로 돌아갑니다.

```shell
$ docker run -d -p 8000:8000 node-docker
ce02b3179f0f10085db9edfccd731101868f58631bdf918ca490ff6fd223a93b
```

Docker started our container in the background and printed the Container ID on the terminal.

> 도커는 컨테이너를 백그라운드로 시작하고 터미널에 Container ID를 출력합니다.

Again, let’s make sure that our container is running properly. Run the same curl command from above.

> 다시한번, 우리의 컨테이너가 제대로 작동하는지 확인해봅시다. 위 처럼 같은 curl 명령어를 수행하세요.

```ignore
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing"}'
{"code":"success","payload":[{"msg":"testing","id":"dc0e2c2b-793d-433c-8645-b3a553ea26de","createDate":"2020-09-01T17:36:09.897Z"}]}
```

### 컨테이너 목록(List containers)

Since we ran our container in the background, how do we know if our container is running or what other containers are
running on our machine? Well, to see a list of containers running on our machine, run `docker ps`. This is similar to
how
the ps command is used to see a list of processes on a Linux machine.

> 우리는 백그라운드로 컨테이너를 실행했기 때문에 우리의 컨테이너가 실행중인지 또는 다른 컨테이너가 실행중인지 어떻게 알까요?
> 실행중인 컨테이너의 목록을 보기 위해서 `docker ps` 명령어를 수행하세요. 이것은 리눅스 머신에서 프로세스들의 목록을 보기 위해 사용하는 ps 명령어와 비슷합니다.

```shell
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ce02b3179f0f        node-docker         "docker-entrypoint.s…"   6 minutes ago       Up 6 minutes        0.0.0.0:8000->8000/tcp   wonderful_kalam
```

The `ps` command tells a bunch of stuff about our running containers. We can see the Container ID, the image running
inside the container, the command that was used to start the container, when it was created, the status, ports that
exposed and the name of the container.

> ps 명령어는 실행 중인 컨테이너에 대한 것들을 말해줍니다.
> 우리는 Container ID, 컨테이너 안에서 실행중인 이미지를 볼 수 있습니다.
> 그리고 컨테이너를 시작하기 위해서 사용되는 명령어도 볼 수 있습니다.
> 컨테이너 생성시간, 상태, 포트 등을 볼수도 있습니다.

You are probably wondering where the name of our container is coming from. Since we didn’t provide a name for the
container when we started it, Docker generated a random name. We’ll fix this in a minute but first we need to stop the
container. To stop the container, run the `docker stop` command which does just that, stops the container. You will need
to pass the name of the container or you can use the container id.

> 여러분들은 컨테이너의 이름이 어떻게 정해지는지 궁금할 것입니다. 우리는 컨테이너를 시작할때 컨테이너의 이름을 준적이 없기 때문입니다.
> 도커는 랜덤한 이름을 생성합니다. 우리는 몇분안에 이것을 고칠 수 있습니다. 하지만 첫번째로 우리는 컨테이너를 중지할 필요가 있습니다.
> 컨테이너를 중지하기 위해서 `docker stop` 명령어를 실행하세요. 여러분들은 컨테이너를 중지하기 위해서 컨테이너의 이름을 전달할 필요가 있습니다. 또는 컨테이너의 ID를 전달할 수 있습니다.

```shell
$ docker stop wonderful_kalam
wonderful_kalam
```

Now rerun the docker ps command to see a list of running containers.

> 지금 실행중인 컨테이너 목록을 보기 위해서 `docker ps` 명령어를 실행하세요.

```ignore
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### 컨테이너 중지, 시작, 이름짓기(Stop, start, and name containers)

Docker containers can be started, stopped and restarted. When we stop a container, it is not removed but the status is
changed to stopped and the process inside of the container is stopped. When we ran the `docker ps` command, the default
output is to only show running containers. If we pass the `--all` or `-a` for short, we will see all containers on our
system whether they are stopped or started.

> 도커 컨테이너들은 시작하고 중지하고 재시작할 수 있습니다. 컨테이너가 중지될때 삭제되지 않습니다. 그러나 status는 정지 상태로 변경됩니다.
> 그리고 컨테이너 안에 프로세스도 정지됩니다. 우리가 `docker ps` 명령어를 실행할때 기본적인 출력은 실행중인 컨테이너를 보여줍니다.
> 만약 우리가 `--all` 또는 줄여서 `-a`를 전달하면, 우리는 우리의 시스템에서 정지되거나 시작하는 컨테이너까지도 모든 컨테이너를 볼 것입니다.

```shell
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
ce02b3179f0f        node-docker         "docker-entrypoint.s…"   16 minutes ago      Exited (0) 5 minutes ago                        wonderful_kalam
ec45285c456d        node-docker         "docker-entrypoint.s…"   28 minutes ago      Exited (0) 20 minutes ago                       agitated_moser
fb7a41809e5d        node-docker         "docker-entrypoint.s…"   37 minutes ago      Exited (0) 36 minutes ago                       goofy_khayyam
```

If you’ve been following along, you should see several containers listed. These are containers that we started and
stopped but have not been removed.

> 계속 따라가다 보면 여러분들은 여러개의 컨테이너가 목록화된것을 볼 수 있습니다. 우리가 시작하고 정지한 컨테이너들입니다. 그러나 삭제되지는 않았습니다.

Let’s restart the container that we just stopped. Locate the name of the container we just stopped and replace the name
of the container below in the restart command.

> 우리가 정지한 컨테이너를 다시 시작합시다. 우리가 방금 정지한 컨테이너의 이름을 위치시키고 restart 명령어에 아래와 같이 컨테이너의 이름을 대체합니다.

```shell
$ docker restart wonderful_kalam
```

Now, list all the containers again using the ps command.

> 지금, 모든 컨테이너의 리스트를 다시한번 ps 명령어를 사용하여 봅니다.

```shell
$ docker ps --all
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                    NAMES
ce02b3179f0f        node-docker         "docker-entrypoint.s…"   19 minutes ago      Up 8 seconds                0.0.0.0:8000->8000/tcp   wonderful_kalam
ec45285c456d        node-docker         "docker-entrypoint.s…"   31 minutes ago      Exited (0) 23 minutes ago                            agitated_moser
fb7a41809e5d        node-docker         "docker-entrypoint.s…"   40 minutes ago      Exited (0) 39 minutes ago                            goofy_khayyam
```

Notice that the container we just restarted has been started in detached mode and has port 8000 exposed. Also, observe
the status of the container is “Up X seconds”. When you restart a container, it will be started with the same flags or
commands that it was originally started with.

> 우리가 방금 재시작한 컨테이너는 백그라운드로 시작하고 8000번 포트를 표시합니다.
> 또한 컨테이너의 상태는 "Up X seconds"라고 표시됩니다. 컨테이너를 재시작할때 컨테이너는 같은 옵션으로 시작하거나 컨테이너가 원래 시작한대로 시작할 것입니다.

Let’s stop and remove all of our containers and take a look at fixing the random naming issue.

> 컨테이너 전체를 정지하고 삭제해봅시다. 그리고 랜덤한 이름 문제를 고쳐봅시다.

Stop the container we just started. Find the name of your running container and replace the name in the command below
with the name of the container on your system.

> 우리가 방금 시작한 컨테이너를 정지합시다. 여러분들의 시작중인 컨테이너의 이름을 찾고 컨테이너의 이름을 아래와 같이 명령어의 이름에서 대체하세요.

```shell
$ docker stop wonderful_kalam
wonderful_kalam
```

Now that all of our containers are stopped, let’s remove them. When a container is removed, it is no longer running nor
is it in the stopped status. However, the process inside the container has been stopped and the metadata for the
container has been removed.

> 우리의 컨테이너 전부가 정지되면 컨테이너들을 삭제해봅시다. 컨테이너를 삭제할때 컨테이너는 더이상 실행중이지 않거나 정지된 상태여야 합니다.
> 그러나 컨테이너안에 프로세스는 정지되고 컨테이너에 대한 메타데이터가 삭제됩니다.

```shell
$ docker ps --all
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                    NAMES
ce02b3179f0f        node-docker         "docker-entrypoint.s…"   19 minutes ago      Up 8 seconds                0.0.0.0:8000->8000/tcp   wonderful_kalam
ec45285c456d        node-docker         "docker-entrypoint.s…"   31 minutes ago      Exited (0) 23 minutes ago                            agitated_moser
fb7a41809e5d        node-docker         "docker-entrypoint.s…"   40 minutes ago      Exited (0) 39 minutes ago                            goofy_khayyam
```

To remove a container, simply run the `docker rm` command passing the container name. You can pass multiple container
names to the command in one command.

> 컨테이너를 삭제하기 위해서 컨테이너 이름을 전달하여 간단하게 `docker rm` 명령어를 실행해봅시다.
> 여러분들은 여러개의 컨테이너 이름을 전달할 수 있습니다.

Again, make sure you replace the containers names in the below command with the container names from your system.

> 다시한번 아래 명령어의 컨테이너 이름을 여러분들의 시스템으로부터의 컨테이너 이름들로 대체하세요.

```shell
$ docker rm wonderful_kalam agitated_moser goofy_khayyam
wonderful_kalam
agitated_moser
goofy_khayyam
```

Run the `docker ps --all` command again to see that all containers are gone.

> 다시한번 `docker ps --all` 명령어를 실행하여 모든 컨테이너들을 보세요.

Now let’s address the pesky random name issue. Standard practice is to name your containers for the simple reason that
it is easier to identify what is running in the container and what application or service it is associated with. Just
like good naming conventions for variables in your code make it simpler to read, so does naming your containers.

> 이제 성가신 무작위 이름 문제를 다뤄봅시다. 일반적으로 컨테이너 이름을 지정하는 것은 컨테이너에서 실행중인 것과 관련된 애플리케이션 또는 서비스를 쉽게 식별할 수 있기 때문입니다.

To name a container, we just need to pass the `--name` flag to the run command.

> 컨테이너에 이름을 짓기 위해서는 우리는 `run` 명령어에 `--name` 옵션을 전달할 필요가 있습니다.

```shell
$ docker run -d -p 8000:8000 --name rest-server node-docker
1aa5d46418a68705c81782a58456a4ccdb56a309cb5e6bd399478d01eaa5cdda
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
1aa5d46418a6        node-docker         "docker-entrypoint.s…"   3 seconds ago       Up 3 seconds        0.0.0.0:8000->8000/tcp   rest-server
```

Now, we can easily identify our container based on the name.

> 지금, 이름을 기반으로한 컨테이너를 쉽게 식별할 수 있습니다.

### 다음 단계(Next steps)

In this module, we took a look at running containers, publishing ports, and running containers in detached mode. We also
took a look at managing containers by starting, stopping, and restarting them. We also looked at naming our containers
so they are more easily identifiable. In the next module, we’ll learn how to run a database in a container and connect
it to our application. See:

> 이번 모듈에서는 우리는 실행 중인 컨테이너, 포트 퍼블리싱, 백그라운드에서 실행중인 컨테이너를 살펴봤습니다.
> 우리는 또한 컨테이너를 시작, 정지, 재시작함으로써 제어하는 것을 살펴봤습니다.
> 우리는 또한 우리의 컨테이너들을 네이밍하는 것을 살펴봤습니다. 그래서 컨테이너들이 더욱 식별이 가능해졌습니다.
> 다음 모듈에서는 우리는 컨테이너에 연결된 데이터베이스를 연결하는 방법에 대해서 배우고 애플리케이션에 연결하는 방법을 배울 것입니다.


