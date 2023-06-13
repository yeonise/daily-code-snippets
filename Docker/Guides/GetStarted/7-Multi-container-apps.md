# Multi container apps

Up to this point, you’ve been working with single container apps. But, now you will add MySQL to the
application stack. The following question often arises - “Where will MySQL run? Install it in the
same container or run it separately?” In general, each container should do one thing and do it well.
The following are a few reasons to run the container separately:

> 지금까지 여러분들은 단일 컨테이너 앱으로 작업하였습니다.
> 그러나 지금은 여러분들은 애플리케이션 스택에 MySQL을 추가할 것입니다.
> 다음 질문은 종종 나옵니다. "MySQL은 어디서 동작하나요? 같은 컨테이너에서 설치하나요? 또는 따로 동작하나요?"
> 일반적으로 각 컨테이너는 한가지 일을 잘해야 합니다.
> 다음은 컨테이너가 따로 동작해야 하는 이유에 대해서 몇가지 설명한 것입니다.

- There’s a good chance you’d have to scale APIs and front-ends differently than databases.
- Separate containers let you version and update versions in isolation.
- While you may use a container for the database locally, you may want to use a managed service for
  the database in production. You don’t want to ship your database engine with your app then.
- Running multiple processes will require a process manager (the container only starts one process),
  which adds complexity to container startup/shutdown.

> - 컨테이너를 따로 동작해서 여러분들이 API들의 스케일을 키울 좋은 기회입니다. 그리고 프론트 엔드와 데이터베이스를 따로 둘 좋은 기회입니다.
> - 별도의 컨테이너를 사용하여 버전 및 업데이트 버전을 분리할 수 있습니다.
> - 로컬에서 데이터베이스 컨테이너를 사용할 수 있지만, 프로덕션 환경에서 데이터베이스라는 관리된 서비스로서 사용할 수 있습니다.
    > 여러분들은 앱에 데이터베이스 엔진이 올려져 있는 것을 원하지 않습니다.
> - 여러 프로세스를 실행하려면 프로세스 관리자(컨테이너가 하나의 프로세스만 시작합니다.)가 필요하므로 컨테이너 시작/종료가 복잡해집니다.

And there are more reasons. So, like the following diagram, it’s best to run your app in multiple
containers.

> 그리고 더 많은 이유들이 있습니다. 따라서 다음 다음 다이어그램과 같이 앱을 여러 컨테이너에서 실행하는 것이 가장 좋습니다.

![](https://docs.docker.com/get-started/images/multi-app-architecture.png)

### 컨테이너 네트워킹(Container networking)

Remember that containers, by default, run in isolation and don’t know anything about other processes
or containers on the same machine. So, how do you allow one container to talk to another? The answer
is networking. If you place the two containers on the same network, they can talk to each other.

> 컨테이너는 기본적으로 다른 프로세스들에 대해서 알지 못하는 또는 같은 컴퓨터에서 컨테이들끼리 격리된 환경에서 동작하는 것을 기억하세요.
> 그래서 하나의 컨테이너가 어떻게 다른 컨테이너와 통신할 수 있는가?
> 그에 대한 대답은 네트워킹입니다. 만약 같은 네트워크에 두개의 컨테이너가 있다면 컨테이너들은 서로 통신할 수 있습니다.

### MySQL 시작하기(Start MySQL)

There are two ways to put a container on a network:

- Assign the network when starting the container.
- Connect an already running container to a network.

> 네트워크에 컨테이너를 넣는 방법은 두가지가 있습니다.
> - 컨테이너를 시작할때 네트워크 할당하기
> - 실행중인 컨테이너가 네트워크에 연결하기

In the following steps, you’ll create the network first and then attach the MySQL container at
startup.

> 다음 단계에서 첫번째로 네트워크를 생성하고 시작시 MySQL 컨테이너를 네트워크에 추가합니다.

1. Create the network.

> 네트워크를 생성합니다.

```shell
$ docker network create todo-app
```

2. Start a MySQL container and attach it to the network. You’re also going to define a few
   environment
   variables that the database will use to initialize the database. To learn more about the MySQL
   environment variables, see the “Environment Variables” section in
   the [MySQL Docker Hub listing](https://hub.docker.com/_/mysql/?_gl=1*nqtoxx*_ga*NTgxNzUzNjcxLjE2ODY0NTkxNzU.*_ga_XJWPQMJYHQ*MTY4NjUzNTAyOC4zLjEuMTY4NjUzNTAzOC41MC4wLjA.)
   .

> MySQL 컨테이너를 시작하고 네트워크에 컨테이너를 첨부합니다.
> 또한 데이터베이스가 초기화하는데 사용할 몇가지 환경변수도 정의합니다.
> MySQL 환경 변수에 대해서 더 배우기 위해서는 MySQL Docker Hub listing에 있는 "Environment Variables" 섹션을 봐주세요.

**Mac/Linux**

```shell
$ docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todds mysql:8.0
```

**Windows**

```shell
docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:8.0
```

In the command above, you’ll see the `--network-alias` flag. In a later section, you’ll learn more
about this flag.

> 위 명령에서 `--network-alias` 플래그를 볼것입니다. 추후 섹션에서 flag에 대해서 배울 것입니다.

**Tip**

You’ll notice a volume named `todo-mysql-data` in the above command that is mounted at
/var/lib/mysql,
which is where MySQL stores its data. However, you never ran a docker volume create command.
Docker recognizes you want to use a named volume and creates one automatically for you.

> 위 명령어에서 MySQL이 데이터를 저장하는 `/var/lib/mysql`에 마운트된 `todo-mysql-data`라는 볼륨을 볼 수 있습니다.
> 그러나 여러분들은 절대로 docker volume create 명령어를 실행하지 않았습니다.
> 도커는 지정된 볼륨을 사용하려는 사용자를 인식하고 자동으로 볼륨을 만듭니다.

3. To confirm you have the database up and running, connect to the database and verify that it
   connects.

> 데이터베이스가 실행중인지 확인하려면 데이터베이스에 연결하고 연결되었는지 확인하세요.

```shell
$ docker exec -it <mysql-container-id> mysql -u root -p
```

When the password prompt comes up, type in secret. In the MySQL shell, list the databases and verify
you see the `todos` database.

> 비밀번호 프롬프트가 나타나면 비밀번호를 입력하세요. MySQL 셸에서 데이터베이스를 리스트하고 `todos` 데이터베이스가 있는지 확인하세요.

```shell
mysql> SHOW DATABASES;
```

You should see output that looks like this:

> 다음과 같은 결과가 나와야 합니다.

```shell
+--------------------+
 | Database           |
 +--------------------+
 | information_schema |
 | mysql              |
 | performance_schema |
 | sys                |
 | todos              |
 +--------------------+
 5 rows in set (0.00 sec)
```

4. Exit the MySQL shell to return to the shell on your machine.

> MySQL 셸을 나가고 여러분들의 컴퓨터의 셸로 돌아가세요.

```shell
mysql> exit
```

You now have a todos database and it’s ready for you to use.

> `todos` 데이터베이스를 가지게 되었고 사용할 준비가 되었습니다.

### MySQL 연결하기(Connect to MySQL)

Now that you know MySQL is up and running, you can use it. But, how do you use it? If you run
another container on the same network, how do you find the container? Remember that each container
has its own IP address.

> 이제 MySQL이 실행되고 있다는 것을 알았으니, MySQL을 사용할 수 있습니다.
> 그러나, 어떻게 사용할 것인가?
> 만약 여러분이 또다른 컨테이너를 같은 네트워크에서 실행한다면 컨테이너를 어떻게 탐색할 것인가?
> 각각의 컨테이너를 IP 주소를 가지고 있는 것을 기억하세요.

To answer the questions above and better understand container networking, you’re going to make use
of the nicolaka/netshoot container, which ships with a lot of tools that are useful for
troubleshooting or debugging networking issues.

> 위 위의 질문에 답하고 컨테이너 네트워킹을 더 잘 이해하기 위해서, 네트워킹 문제를 해결하거나 디버깅하는데
> 유용한 많은 도구와 함께 제공되는 nicolaka/netshoot 컨테이너를 사용하게 됩니다.

1. Start a new container using the nicolaka/netshoot image. Make sure to connect it to the same
   network.

> nicolaka/entshoot 이미지를 사용하여 새로운 컨테이너를 시작하세요.
> 컨테이너는 동일한 네트워크에 연결해야 합니다.

```shell
$ docker run -it --network todo-app nicolaka/netshoot
```

2. Inside the container, you’re going to use the `dig` command, which is a useful DNS tool. You’re
   going to look up the IP address for the hostname `mysql`.

> 컨테이너안에서 유용한 DNS 도구인 dig 명령어를 사용하세요.
> 명령어를 실행하면 호스트이름 mysql에 대한 IP 주소를 확인할 것입니다.

```shell
$ dig mysql
```

You should get output like the following.

> 명령어 실행시 다음과 같은 결과를 볼 것입니다.

```shell
; <<>> DiG 9.18.8 <<>> mysql
 ;; global options: +cmd
 ;; Got answer:
 ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32162
 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

 ;; QUESTION SECTION:
 ;mysql.				IN	A

 ;; ANSWER SECTION:
 mysql.			600	IN	A	172.23.0.2

 ;; Query time: 0 msec
 ;; SERVER: 127.0.0.11#53(127.0.0.11)
 ;; WHEN: Tue Oct 01 23:47:24 UTC 2019
 ;; MSG SIZE  rcvd: 44
```

In the “ANSWER SECTION”, you will see an `A` record for `mysql` that resolves to `172.23.0.2` (your
IP
address will most likely have a different value). While `mysql` isn’t normally a valid hostname,
Docker was able to resolve it to the IP address of the container that had that network alias.
Remember, you used the `--network-alias` earlier.

> "ANSWER SECTION"에서 `172.23.0.2`로 확인되는 `mysql`에 대한 `A` 레코드를 볼수 있습니다.(IP 주소의 값이 다를 가능성 높습니다.)
> `mysql`은 일반적으로 유효한 호스트이름은 아닌 반면에 도커는 네트워크 별칭을 가진 컨테이너의 IP 주소로 확인할 수 있습니다.
> 기억하세요. 여러분들은 앞서 `--network-alias`를 사용하였습니다.

What this means is that your app only simply needs to connect to a host named mysql and it’ll talk
to the database.

> 이 의미는 여러분들의 앱이 오직 간단하게 mysql이라는 이름의 호스트에 연결하기만 하면 데이터베이스 연결된다는 의미입니다.

### MySQL과 같이 앱 실행하기(Run your app with MySQL)

The todo app supports the setting of a few environment variables to specify MySQL connection
settings. They are:

- MYSQL_HOST - the hostname for the running MySQL server
- MYSQL_USER - the username to use for the connection
- MYSQL_PASSWORD - the password to use for the connection
- MYSQL_DB - the database to use once connected

> todo 앱은 MySQL 연결 설정을 명세하기 위해 몇가지 환경 변수의 설정을 지원합니다. 그것들은 다음과 같습니다.
> - MYSQL_HOST : 실행중인 MySQL 서버에 대한 호스트이름
> - MYSQL_USER : 연결을 위해 사용되는 유저 이름
> - MYSQL_PASSWORD : 연결을 위해 사용되는 패스워드
> - MYSQL_DB : 연결되자마자 사용되는 데이터베이스 이름

**Note**

While using env vars to set connection settings is generally accepted for development, it’s highly
discouraged when running applications in production. Diogo Monica, a former lead of security at
Docker, [wrote a fantastic blog](https://blog.diogomonica.com//2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)
post explaining why.

> **참고**
>
> 연결 설정을 설정하기 위해 환경 변수들을 사용하는 것은 개발을 위해서 일반적으로 수용되는 반면,
> 운영 환경에서 애플리케이션을 실행할 때는 권장되지 않습니다. 도커의 보안 책임자였던 디오고 모니카는 그 이유를 설명하는 블로그 게시물을 작성하였습니다.

A more secure mechanism is to use the secret support provided by your container orchestration
framework. In most cases, these secrets are mounted as files in the running container. You’ll see
many apps (including the MySQL image and the todo app) also support env vars with a `_FILE` suffix
to
point to a file containing the variable.

> 보다 안전한 메커니즘은 컨테이너 오케스트레이션 프레임웤에 의해 제공되는 암호 지원을 사용하는 것입니다.
> 대부분의 경우에 이 암호들은 실행중인 컨테이너 안에 파일로서 마운트됩니다.
> 변수가 포함된 파일을 가리키는 _FILE 접미사를 가진 변수를 지원하는 많은 앱(MySQL 이미지 및 작업관리 앱 포함)도 볼수 있습니다.

As an example, setting the `MYSQL_PASSWORD_FILE` var will cause the app to use the contents of the
referenced file as the connection password. Docker doesn’t do anything to support these env vars.
Your app will need to know to look for the variable and get the file contents.

> 예제로서 MYSQL_PASSWORD_FILE 변수를 설정하면 앱에서 참조된 파일의 내용을 연결 암호로 사용합니다.
> 도커는 이러한 환경 변수에 대해서 지원하기 위해 어떤 것도 하지 않습니다.
> 여러분들의 앱은 변수와 파일 내용들을 얻는 것을 필요하게 될 것입니다.

You can now start your dev-ready container.

> 여러분들은 개발준비된 컨테이너를 시작할 수 있습니다.

Specify each of the environment variables above, as well as connect the container to your app
network. Make sure that you are in the getting-started/app directory when you run this command.

> 앱 네트워크에 컨테이너 연결뿐만 아니라 위 환경 변수를 각각 명세하세요.
> 이 명령어를 실행했을 때 `getting-started/app` 디렉토리를 확인하세요.

**Mac/Linux**

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

**Windows**

```shell
$ docker run -dp 3000:3000 `
   -w /app -v "$(pwd):/app" `
   --network todo-app `
   -e MYSQL_HOST=mysql `
   -e MYSQL_USER=root `
   -e MYSQL_PASSWORD=secret `
   -e MYSQL_DB=todos `
   node:18-alpine `
   sh -c "yarn install && yarn run dev"
```

2. If you look at the logs for the container (`docker logs -f <container-id>`), you should see a
   message similar to the following, which indicates it’s using the mysql database.

> 만약 컨테이너에(`docker logs -f <container-id>`) 대한 로그를 보고 싶다면 mysql 데이터베이스를 사용하는 것을 가리키는 다음과 비슷한 메시지를
> 보아야 합니다.

```shell
$ nodemon src/index.js
 [nodemon] 2.0.20
 [nodemon] to restart at any time, enter `rs`
 [nodemon] watching dir(s): *.*
 [nodemon] starting `node src/index.js`
 Connected to mysql db at host mysql
 Listening on port 3000
```

3. Open the app in your browser and add a few items to your todo list.

> 브라우저에 앱을 열고 여러분들의 작업 관리 리스트에 몇가지 아이템을 추가하세요.

4. Connect to the mysql database and prove that the items are being written to the database.
   Remember, the password is `secret`.

> mysql 데이터베이스에 연결하고 데이터베이스에 작성된 아이템들을 확인합니다.
> 기억하세요. 패스워드는 `secret`입니다.

```shell
$ docker exec -it <mysql-container-id> mysql -p todos
```

And in the mysql shell, run the following:

> 그리고 mysql 셸에서 다음과 같이 실행합니다.

```shell
mysql> select * from todo_items;
 +--------------------------------------+--------------------+-----------+
 | id                                   | name               | completed |
 +--------------------------------------+--------------------+-----------+
 | c906ff08-60e6-44e6-8f49-ed56a0853e85 | Do amazing things! |         0 |
 | 2912a79e-8486-4bc3-a4c5-460793a575ab | Be awesome!        |         0 |
 +--------------------------------------+--------------------+-----------+
```

Your table will look different because it has your items. But, you should see them stored there.

> 테이블은 여러분들의 아이템과 다릅니다. 여러분들은 각자 저장한 아이템들을 볼 것입니다.

### 다음 단계(Next steps)

At this point, you have an application that now stores its data in an external database running in a
separate container. You learned a little bit about container networking and service discovery using
DNS.

> 이번 장에서는 여러분들은 별도의 컨테이너에서 실행중인 외부의 데이터베이스에 데이터를 저장하는 애플리케이션을 가져보았습니다.
> 여러분들은 컨테이너 네트워킹에 대해 조금 배웠고 DNS를 사용한 서비스 디커버리를 배웠습니다.

But, there’s a good chance you are starting to feel a little overwhelmed with everything you need to
do to start up this application. You have to create a network, start containers, specify all of the
environment variables, expose ports, and more! That’s a lot to remember and it’s certainly making
things harder to pass along to someone else.

> 그러나 이 애플리케이션을 시작하는데 필요한 모든 작업에 대해서 약간 부담을 느끼기 시작할 가능성이 있습니다.
> 여러분들은 네트워크를 생성해야 하고 컨테이너를 시작해야 하고 환경변수, 외부 포트 등 모든 것을 명세해야 합니다.
> 많은 것들을 기억해야 하고 다른 사람에게 전달하는 것을 확실히 어렵게 만들고 있습니다.

In the next section, you’ll learn about Docker Compose. With Docker Compose, you can share your
application stacks in a much easier way and let others spin them up with a single, simple command.

> 다음 장에서는 Docker Compose에 대해서 배울 것입니다. Docker Compose를 가지고 더욱 쉬운 방법으로 애플리케이션 스택을 공유할 수 있습니다.
> 그리고 다른 사용자가 단일하고 간단한 명령어로 스택을 회전시킬 수 있습니다.
