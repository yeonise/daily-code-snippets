# Use Docker Compose

Docker Compose is a tool that was developed to help define and share multi-container applications.
With Compose, we can create a YAML file to define the services and with a single command, can spin
everything up or tear it all down.

> 도커 컴포즈는 멀티 컨테이너 애플리케이션들을 공유하고 정의하는데 도와주기 위해서 개발된 도구입니다.
> 컴포즈를 사용하면 YAML 파일을 생성하여 서비스를 정의하고 단일 명령어 하나로 컨테이너들을 돌리거나 모두 해제할 수 있습니다.

The big advantage of using Compose is you can define your application stack in a file, keep it at
the root of your project repo (it’s now version controlled), and easily enable someone else to
contribute to your project. Someone would only need to clone your repo and start the compose app. In
fact, you might see quite a few projects on GitHub/GitLab doing exactly this now.

So, how do we get started?

> 컴포즈를 사용할때 큰 장점은 프로젝트 저장소의 루트 경로에 저장하는 파일에 애플리케이션 스택을 정의할 수 있습니다.
> 그리고 다른 사람에게 여러분들의 프로젝트를 쉽게 전달할 수 있습니다.
> 누군가가 여러분들의 저장소를 복사하고 컴포즈 앱을 실행하기만 하면 됩니다.
> 실제로 여러분들은 GitHub/GitLab에서 이와 같은 작업을 수행하는 프로젝트가 많이 있습니다.
> 그래서 우리는 어떻게 시작해야 할까요?

### 도커 컴포즈 설치하기(Install Docker Compose)

If you installed Docker Desktop for Windows, Mac, or Linux you already have Docker Compose!
Play-with-Docker instances already have Docker Compose installed as well.

> 왼도우, 맥, 리눅스에 도커 데스크톱이 이미 설치되어 있다면 이미 도커 컴포즈는 준비되어 있습니다.!
> Play-with-Docker 인스턴스는 이미 도커 컴포즈가 설치되어 있습니다.

Standalone installations of Docker Engine requires Docker Compose to be installed as a separate
package, see [Install the Compose plugin](https://docs.docker.com/compose/install/linux/).

> 도커 엔진을 독립 실행형으로 설치하려면 도커 컴포즈를 별도의 패키지로 설치해야 합니다.
> 컴포즈 플러그인을 설치를 참조해주세요.

After installation, you should be able to run the following and see version information.

> 설치후에 여러분들은 다음 명령어를 실행하여 버전 정보 확인이 가능합니다.

```shell
$ docker compose version
```

### 컴포즈 파일 생성하기(Create the Compose file)

1. At the root of the /getting-started/app folder, create a file named docker-compose.yml.

> `/getting-started/app` 디렉토리의 루트 경로에서 `docker-compose.yml` 파일을 생성합니다.

2. In the compose file, we’ll start off by defining the list of services (or containers) we want to
   run as part of our application.

> 컴포즈 파일에서 애플리케이션의 부분으로서 실행하기 위해서 서비스들(또는 컨테이너들)의 목록을 정의하는 것으로 시작합니다.

```shell
services:
```

And now, we’ll start migrating a service at a time into the compose file.

> 그리고 지금, 한번에 서비스를 컴포즈 파일로 마이그레이션하기 시작합니다.

### 앱 서비스 정의하기(Define the app service)

To remember, this was the command we were using to define our app container.

> 이 명령어는 앱 컨테이너를 정의하기 위해서 사용하는 명령어입니다.

```shell
$ docker run -dp 3000:3000 \
  -w /app -v "$(pwd):/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:18-alpine \
  sh -c "yarn install && yarn run dev"
```

1. First, let’s define the service entry and the image for the container. We can pick any name for
   the service. The name will automatically become a network alias, which will be useful when
   defining our MySQL service.

> 첫번째로 컨테이너에 대한 서비스 항목과 이미지를 정의합니다.
> 우리는 서비스 이름을 선택할 수 있습니다. 그 서비스 이름은 자동적으로 MySQL 서비스를 정의할 때 유용한 네트워크 별칭이 될 것입니다.

```shell
services:
  app:
    image: node:18-alpine
```

2. Typically, you will see the command close to the image definition, although there is no
   requirement on ordering. So, let’s go ahead and move that into our file.

> 일반적으로 요구사항에 대한 순서는 없지만 이미지 정의에 가까운 명령어를 볼 수 있습니다.
> 그래서 이제 파일로 이동해 보겠습니다.

```shell
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
```

3. Let’s migrate the `-p 3000:3000` part of the command by defining the `ports` for the service. We
   will use
   the [short syntax](https://docs.docker.com/compose/compose-file/05-services/#short-syntax-3)
   here, but there is also a more
   verbose [long syntax](https://docs.docker.com/compose/compose-file/05-services/#long-syntax-3)
   available as well.

> 서비스의 포트를 정의하여 명령어의 `-p 3000:3000` 부분을 마이그레이션합니다. 마이그레이션 함으로써 터미널 명령에서 정의한 포트 설정을 파일에 정의합니다.
> 우리는 short syntax를 사용할 것입니다. 그러나 거기에는 더 많은 long syntax도 사용할 수 있습니다.

```shell
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
```

4. Next, we’ll migrate both the working directory (`-w /app`) and the volume mapping (`-v "$(pwd):
   /app"`) by using the `working_dir` and `volumes` definitions. Volumes also has
   a [short](https://docs.docker.com/compose/compose-file/05-services/#short-syntax-5)
   and [long](https://docs.docker.com/compose/compose-file/05-services/#long-syntax-5)
   syntax.

> 다음으로 우리는 작업 디렉토리(`-w /app`)와 볼륨 매핑(`-v "$(pwd):/app"`)을 `working_dir`와 `volumes` 정의를 사용하여 마이그레이션할
> 것입니다. 볼륨 또한 짧고 긴 문법을 가지고 있습니다.

One advantage of Docker Compose volume definitions is we can use relative paths from the current
directory.

> 도커 컴포즈 볼륨 정의의 한가지 장점은 현재 디렉토리로부터 상대 경로를 사용할 수 있다는 점입니다.

```shell
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
```

5. Finally, we need to migrate the environment variable definitions using the environment key.

> 마지막으로 우리는 `environment` 키를 사용하여 환경 변수 정의를 마이그레이션 해야 합니다.

```shell
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
```

### MySQL 서비스 정의하기(Define the MySQL service)

Now, it’s time to define the MySQL service. The command that we used for that container was the
following:

> MySQL 서비스를 정의할 시간이 되었습니다. MySQL 컨테이너를 대상으로 우리가 사용한 명령어는 다음과 같았습니다.

```shell
$ docker run -d \
  --network todo-app --network-alias mysql \
  -v todo-mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=todos \
  mysql:8.0
```

1. We will first define the new service and name it mysql so it automatically gets the network
   alias. We’ll go ahead and specify the image to use as well.

> 새로운 서비스를 정의하고 이름을 mysql로 지정하여 네트워크 별칭을 자동으로 가져옵니다.
> 사용할 이미지도 지정합니다.

```yaml
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
```

2. Next, we’ll define the volume mapping. When we ran the container with docker run, the named
   volume was created automatically. However, that doesn’t happen when running with Compose. We need
   to define the volume in the top-level volumes: section and then specify the mountpoint in the
   service config. By simply providing only the volume name, the default options are used. There are
   [many more options available](https://docs.docker.com/compose/compose-file/07-volumes/) though.

> 다음으로 볼륨 매핑을 정의합니다. `docker run` 명령어를 가지고 실행할 때 이름붙인 볼륨이 자동으로 생성되었습니다.
> 그러나 컴포즈를 가지고 실행할때는 발생하지 않습니다. 우리는 탑 레벨에서 `volumes:`을 가지고 볼륨 정의가 필요합니다.
> 그리고 나서 서버 설정에 마운트 포인트를 명세합니다.
> 오직 볼륨 이름을 간단하게 제공함으로써 기본적인 옵션들이 사용됩니다.
> 거기에는 많은 이용가능한 옵션들이 있습니다.

```yaml
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql

volumes:
  todo-mysql-data:
```

3. Finally, we only need to specify the environment variables.

> 마지막으로 환경 변수들의 명세가 필요합니다.

```yaml
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

At this point, our complete docker-compose.yml should look like this:

> 완성된 `docker-compose.yml`은 다음과 같습니다.

```yaml
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

### 애플리케이션 스택 실행하기(Run the application stack)

Now that we have our `docker-compose.yml` file, we can start it up!

> docker-compose.yml 파일을 가지고 시작해볼 수 있습니다.!

1. Make sure no other copies of the app/db are running first (`docker ps` and `docker rm -f <ids>`).

> 다른 app/db의 복사본들이 실행중인지 우선 확인해보세요(`docker ps`와 `docker rm -f <ids>` 명령어를 활용해보세요.).

2. Start up the application stack using the `docker compose up` command. We’ll add the -d flag to
   run everything in the background.

> `docker compose up` 명령어를 사용하여 애플리케이션 스택을 시작하세요.
> `-d` 옵션을 추가하여 백그라운드에서 실행하도록 합니다.

```shell
$ docker compose up -d
```

When we run this, we should see output like this:

> 애플리케이션 스택이 실행할때 다음과 같은 결과를 볼것 입니다.

```shell
Creating network "app_default" with the default driver
 Creating volume "app_todo-mysql-data" with default driver
 Creating app_app_1   ... done
 Creating app_mysql_1 ... done
```

You’ll notice that the volume was created as well as a network! By default, Docker Compose
automatically creates a network specifically for the application stack (which is why we didn’t
define one in the compose file).

> 여러분들은 네트워크뿐만 아니라 볼륨이 생성되었다는 것을 알림받을 것입니다.
> 기본적으로 도커 컴포즈는 자동적으로 애플리케이션 스택 전용 네트워크를 생성합니다. 이 때문에 컴포즈 파일에서 네트워클르 정의하지 않습니다.

3. Let’s look at the logs using the `docker compose logs -f` command. You’ll see the logs from each
   of the services interleaved into a single stream. This is incredibly useful when you want to
   watch for timing-related issues. The `-f` flag “follows” the log, so will give you live output as
   it’s generated.

> `docker compose logs -f` 명령어를 사용하여 로그를 봅니다.
> 각각의 서비스들이 싱글 스트림으로 인터리브(어떤 프로그램의 일부분을 다른 프로그램에 끼워 넣는 일.)되어 표시됩니다.
> 이 명령어는 티이밍 관련 이슈에 대해서 보고자 할때 유용합니다.
> -f 옵션은 로그를 "따라가기" 때문에 실시간 출력을 그대로 출력합니다.

If you have run the command already, you’ll see output that looks like this:

> 만약 명령어가 실행할 준비되면 다음과 같은 결과를 볼 것입니다.

```shell
mysql_1  | 2019-10-03T03:07:16.083639Z 0 [Note] mysqld: ready for connections.
 mysql_1  | Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
 app_1    | Connected to mysql db at host mysql
 app_1    | Listening on port 3000
```

The service name is displayed at the beginning of the line (often colored) to help distinguish
messages. If you want to view the logs for a specific service, you can add the service name to the
end of the logs command (for example, docker compose logs -f app).

> 메시지 구분을 도와주기 위해서 서비스 이름이 줄(종종 색깔로 표시됨)의 시작부분에 표시됩니다.
> 만약 특정 서비스에 대한 로그를 보고자 한다면 logs 명령어의 마지막에 서비스 이름을 추가하여 볼 수 있습니다.
> 예를 들어 `docker compose logs -f app`와 같습니다.

4. At this point, you should be able to open your app and see it running. And hey! We’re down to a
   single command!

> 이 시점에서 앱을 열수 있고 실행하는 것을 볼 수 있습니다. 그리고 단일 명령어로 멈추게 할 수 있습니다.

### 도커 대시보드에서 앱 스택 보기(See the app stack in Docker Dashboard)

If we look at the Docker Dashboard, we’ll see that there is a group named app. This is the “project
name” from Docker Compose and used to group the containers together. By default, the project name is
simply the name of the directory that the docker-compose.yml was located in.

> 만약 도커 대시보드를 본다면 app이라는 그룹이 있습니다. 이것은 도커 컴포즈의 "프로젝트 이름"이며 컨테이너를 그룹화하는데 사용됩니다.
> 기본적으로 프로젝트 이름은 간단하게 docker-compse.yml 파일이 위치한 디렉토리의 이름입니다.

![](https://docs.docker.com/get-started/images/dashboard-app-project-collapsed.png)

If you click the disclose arrow next to app, you will see the two containers we defined in the
compose file. The names are also a little more descriptive, as they follow the pattern of <
service-name>-<replica-number>. So, it’s very easy to quickly see what container is our app and
which container is the mysql database.

> app 옆에 있는 화살표를 클릭하면 컴포즈 파일에 정의된 두개의 컨테이너를 볼 것입니다.
> 컨테이너 이름은 <서비스명>-<복제번호> 패턴을 따르기 때문에 컨테이너 이름들이 더 설명적입니다.
> 그래서 어떤 컨테이너가 app이고 어떤 컨테이너가 mysql 데이터베이스 컨테이너인지 빠르고 쉽게 확인할 수 있습니다.

![](https://docs.docker.com/get-started/images/dashboard-app-project-expanded.png)

### 모든 컨테이너 다운시키기(Tear it all down)

When you’re ready to tear it all down, simply run `docker compose down` or hit the trash can on the
Docker Dashboard for the entire app. The containers will stop and the network will be removed.

> 여러분들이 모든 컨테이너를 다운시킬 준비가 될때 간단하게 `docker compose down` 명령어를 실행하거나 도커 대시보드에 전체 앱에 대한 휴지통 버튼을 클릭하면
> 됩니다.
> 컨테이너들은 전부 멈출 것이고 네트워크도 삭제될 것입니다.

Warning

Removing Volumes

By default, named volumes in your compose file are NOT removed when running docker compose down. If
you want to remove the volumes, you will need to add the --volumes flag.

The Docker Dashboard does not remove volumes when you delete the app stack.

> 경고
>
> 볼륨 삭제하기
>
> 기본적으로 컴포즈 파일에 있는 이름있는 볼륨들은 docker compose down 수행할때 삭제되지 않습니다.
> 만약 볼륨을 삭제하고 싶다면 `--volumes` 옵션을 추가해야 합니다.
>
> 도커 대시보드 또한 앱 스택을 삭제할 때 볼륨을 삭제하지 않습니다.

Once torn down, you can switch to another project, run docker compose up and be ready to contribute
to that project! It really doesn’t get much simpler than that!

> 일단 컨테이너들이 다운되면 또다른 프로젝트로 바꿀수 있습니다.
> `docker compose up`을 실행하고 프로젝트에 기여할 준비를 하세요!
> 그것은 간단하지 않습니다.

### 다음 단계(Next steps)

In this section, you learned about Docker Compose and how it helps you dramatically simplify the
defining and sharing of multi-service applications. You created a Compose file by translating the
commands you were using into the appropriate compose format.

> 이번 장에서는 여러분들은 도커 컴포즈에 대해서 배웠고 도커 컴포즈가 다중 서비스 애플리케이션의 정의 및 공유를 획기적으로 간소화하는 방법에 대해서 배웠습니다.
> 여러분들이 사용하는 도커 실행 명령어들을 적절한 컴포즈 형식으로 변환하여 컴포즈 파일에 작성해봤습니다.

At this point, you’re starting to wrap up the tutorial. However, there are a few best practices
about image building you should cover, as there is a big issue with the Dockerfile you’ve been
using.

> 이 시점에서 여러분들은 튜토리얼을 마무리하는 중입니다. 그러나 거기에는 이미지 빌드에 대한 몇가지 권장 사례들이 있습니다.
> 여러분들이 사용하는 Dockerfile은 큰 문제가 있습니다.




