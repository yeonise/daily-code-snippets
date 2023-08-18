## Use containers for development

### 선행조건(Prerequisites)

Work through the steps to build an image and run it as a containerized application
in [Run your image as a container](https://docs.docker.com/language/nodejs/run-containers/).

> 이미지를 빌드하는 단계를 작업하고 이미지를 컨테이너로서 실행하는 것에서 컨테이너화된 애플리케이션을 실행하는 것을 실습하세요.

### 소개(Introduction)

In this module, we’ll walk through setting up a local development environment for the application we built in the
previous modules. We’ll use Docker to build our images and Docker Compose to make everything a whole lot easier.

> 이번장에서는 우리는 이전 장에서 우리가 빌드한 애플리케이션에 대하여 로컬 개발 환경을 설정하는 것을 할 것입니다.
> 우리는 우리의 이미지들을 빌드하기 위해서 도커를 사용할 것이고 Docker Compose를 사용하여 모든 것을 훨씬 쉽게 만들 것입니다.

### 지역 데이터베이스와 컨테이너(Local database and containers)

First, we’ll take a look at running a database in a container and how we use volumes and networking to persist our data
and allow our application to talk with the database. Then we’ll pull everything together into a compose file which will
allow us to setup and run a local development environment with one command. Finally, we’ll take a look at connecting a
debugger to our application running inside a container.

> 첫번째로 우리는 컨테이너에서 데이터베이스를 실행하는 것에 대해서 살펴볼 것이고 우리가 어떻게 볼륨을 사용할 것인지, 우리의 데이터를 보존하기 위해서 네트워킹을 어떻게 사용할 것인지 살펴봅니다.
> 그리고 데이터베이스와 우리의 애플리케이션이 소통하는 것을 허용하는 것을 어떻게 할지 살펴봅니다.
> 그러고 나서 우리는 모든 것을 같이 컴포즈 파일로 내려받을 것이고 그 컴포즈 파일은 하나의 명령어로 로컬 개발 환경을 설정하고 실행할 수 있습니다.
> 마지막으로 우리는 컨테이너 안에서 실행중인 애플리케이션에서 디버거에 연결하는 것을 살펴볼 것입니다.

Instead of downloading MongoDB, installing, configuring and then running the Mongo database as a service, we can use the
Docker Official Image for MongoDB and run it in a container.

> MongoDB를 다운로드하는 대신 Mongo 데이터베이스를 서비스로서 설치, 구성 및 실행합니다.
> 우리는 MongoDB에 대한 도커 공식 이미지를 사용하고 컨테이너에서 실행할 수 있습니다.

Before we run MongoDB in a container, we want to create a couple of volumes that Docker can manage to store our
persistent data and configuration. Let’s use the managed volumes feature that docker provides instead of using bind
mounts. For more information, see [Use volumes](https://docs.docker.com/storage/volumes/).

> 컨테이너에서 MongoDB를 실행하기 전에 우리는 도커가 데이터와 설정을 저장하기 위해 관리할 수 있는 두개의 볼륨을 생성하기를 원합니다.
> bind mounts를 사용하는 대신에 도커가 제공하는 관리된 볼륨 기능을 사용해봅시다.
> 더 많은 정보는 볼륨 사용하기를 참고해주세요.

Let’s create our volumes now. We’ll create one for the data and one for configuration of MongoDB.

> 지금 볼륨들을 생성해봅시다. 우리는 MonoDB의 설정과 데이터에 대한 볼륨을 생성할 것입니다.

```shell
$ docker volume create mongodb
$ docker volume create mongodb_config
```

Now we’ll create a network that our application and database will use to talk with each other. The network is called a
user-defined bridge network and gives us a nice DNS lookup service which we can use when creating our connection string.

> 지금 우리는 우리의 애플리케이션과 데이터베이스가 서로 소통하기 위해서 사용할 네트워크를 생성할 것입니다.
> 네트워크는 사용자 정의 브릿지 네트워크라고 부릅니다. 그리고 우리에게 연결 문자열을 생성할때 우리가 사용할 수 있는 휼륭한 DNS lookup 서비스를 줍니다.

```shell
$ docker network create mongodb
```

Now we can run MongoDB in a container and attach to the volumes and network we created above. Docker will pull the image
from Hub and run it for you locally.

> 지금 우리는 컨테이너에서 MongoDB를 실행할 수 있고 우리가 위에서 생성한 볼륨들과 네트워크를 붙일 수 있습니다.
> 도커는 허브로부터 이미지를 내려받고 지역적으로 실행할 것입니다.

```shell
$ docker run -it --rm -d -v mongodb:/data/db \
  -v mongodb_config:/data/configdb -p 27017:27017 \
  --network mongodb \
  --name mongodb \
  mongo
```

Okay, now that we have a running MongoDB, let’s update server.js to use MongoDB and not an in-memory data store.

> 좋습니다. 지금 우리는 MongoDB 실행을 하였습니다. MonboDB를 사용하기 위해서 server.js를 업데이트하고 인 메모리 데이터 저장소를 사용하지 않게 합니다.

```shell
const ronin 		= require( 'ronin-server' )
const database  = require( 'ronin-database' )
const mocks 		= require( 'ronin-mocks' )

async function main() {

    try {
    await database.connect( process.env.CONNECTIONSTRING )
    
    const server = ronin.server({
            port: process.env.SERVER_PORT
        })

        server.use( '/', mocks.server( server.Router()) )

    const result = await server.start()
        console.info( result )
    
    } catch( error ) {
        console.error( error )
    }
}

main()
```

We’ve added the `ronin-database` module and we updated the code to connect to the database and set the in-memory flag to
false. We now need to rebuild our image so it contains our changes.

> 우리는 ronin-database 모듈을 추가하였고 데이터베이스와 연결하기 위한 코드를 갱신하였고 인 메모리 옵션을 false로 설정하였습니다.
> 우리는 지금 이미지 재빌드가 필요합니다.

First let’s add the ronin-database module to our application using npm.

> 첫번째로 npm을 사용하여 애플리케이션에 ronin-database 모듈을 추가합시다.

```shell
$ npm install ronin-database
```

Now we can build our image.

> 지금 우리는 이미지를 빌드할 수 있습니다.

```shell
$ docker build --tag node-docker .
```

Now, let’s run our container. But this time we’ll need to set the `CONNECTIONSTRING` environment variable so our
application knows what connection string to use to access the database. We’ll do this right in the `docker run` command.

> 지금, 커넽이너를 시작해봅시다. 그러나 이 시점에서 우리는 CONNECTIONSTRING 환경 변수를 설정할 필요가 있습니다.
> 우리의 애플리케이션이 데이터베이스에 접근하기 위해서 사용하는 연결 문자열이 무엇인지 알게 하기 위해서입니다.
> 우리는 `docker run` 명령어에서 전달할 것입니다.

```shell
$ docker run \
  -it --rm -d \
  --network mongodb \
  --name rest-server \
  -p 8000:8000 \
  -e CONNECTIONSTRING=mongodb://mongodb:27017/notes \
  node-docker
```

The `notes` at the end of the connection string is the desired name for our database.

> 연결 문자열의 마지막에 `notes`는 데이터베이스의 이름입니다.

Let’s test that our application is connected to the database and is able to add a note.

> 데이터베이스에 연결된 애플리케이션을 테스트해봅시다.

```shell
$ curl --request POST \
  --url http://localhost:8000/notes \
  --header 'content-type: application/json' \
  --data '{"name": "this is a note", "text": "this is a note that I wanted to take while I was working on writing a blog post.", "owner": "peter"}'
```

You should receive the following json back from our service.

> 서비스로부터 다음과 같은 json을 응답받을 것입니다.

```json
{"code":"success","payload":{"_id":"5efd0a1552cd422b59d4f994","name":"this is a note","text":"this is a note that I wanted to take while I was working on writing a blog post.","owner":"peter","createDate":"2020-07-01T22:11:33.256Z"}}
```

### 지역적으로 개발하기 위해서 Compose 사용하기(Use Compose to develop locally)

In this section, we’ll create a Compose file to start our node-docker and the MongoDB with one command. We’ll also set
up the Compose file to start the node-docker in debug mode so that we can connect a debugger to the running node
process.

> 이번장에서 우리는 node-docker와 MongoDB를 단일 명령어로 시작하기 위해서 Compose 파일을 작성할 것입니다.
> 우리는 또한 노드 프로세스를 실행하는 것에 대한 디버거에 연결할 수 있도록 디버그 모드에서 node-docker를 시작하기 위해서 Compose 파일을 설정할 것입니다.

Open the notes-service in your IDE or text editor and create a new file named docker-compose.dev.yml. Copy and paste the
below commands into the file.

> IDE 또는 텍스트 편집기에서 notes-service를 열고 docker-compose.dev.yml 파일을 생성하세요.
> 다음 내용을 파일에 복사 및 붙여넣기 하세요.

```yml
version: '3.8'

services:
 notes:
  build:
   context: .
  ports:
   - 8000:8000
   - 9229:9229
  environment:
   - SERVER_PORT=8000
   - CONNECTIONSTRING=mongodb://mongo:27017/notes
  volumes:
   - ./:/app
   - /app/node_modules
  command: npm run debug

 mongo:
  image: mongo:4.2.8
  ports:
   - 27017:27017
  volumes:
   - mongodb:/data/db
   - mongodb_config:/data/configdb
volumes:
 mongodb:
 mongodb_config:
```

This Compose file is super convenient as we do not have to type all the parameters to pass to the docker run command. We
can declaratively do that in the Compose file.

> Compose 파일은 docker run 명령어에 전달하기 위해 모든 파라미터를 입력할 필요가 없기 때문에 매우 편리합니다.
> 우리는 Compose 파일에서 선언적으로 수행할 수 있습니다.

We are exposing port 9229 so that we can attach a debugger. With volumes, we are also mapping our local source code into
the running container so that we can make changes in our text editor and have those changes picked up in the container.

> 우리는 디버거를 붙일수 있도록 9229 포트를 볼륨과 함께 노출합니다.
> 우리는 또한 지역 소스 코드를 실행 중인 컨테이너에 우리가 텍스트 편집기에 변경할 수 있도록 그리고 컨테이너에서 변경사항들을 픽업할 수 있도록 매핑합니다.

One other really cool feature of using a Compose file is that we have service resolution set up to use the service
names. So we are now able to use “mongo” in our connection string. The reason we use mongo is because that is what we
have named our MongoDB service in the Compose file as.

> Compose 파일을 사용하는 다른 하나의 기능은 서비스 이름들을 사용하기 위해서 서비스 해결 방법을 설정했다는 것입니다.
> 그래서 우리는 우리의 연결 문자열에 "mongo"라고 사용이 가능합니다.
> 우리가 mongo를 사용하는 이유는 Compose 파일에서 MongoDB 서비스의 이름이 MONGO이기 때문입니다.

To start our application in debug mode, we need to add a line to our package.json file to tell npm how to start our
application in debug mode.

> 애플리케이션을 디버그 모드에서 시작하기 위해서 우리는 package.json 파일에 줄을 추가해야 합니다. package.json 파일은 npm에게 우리의 애플리케이션을 디버그 모드로 시작하겠다고 말합니다.

Open the package.json file and add the following line to the scripts section:

> package.json 파일을 열고 scripts 섹션에 다음 라인을 추가합니다.

```json
"debug": "nodemon --inspect=0.0.0.0:9229 -L server.js"
```

As you can see, we are going to use nodemon. Nodemon starts our server in debug mode and also watches for files that
have changed, and restarts our server. Let’s run the following command in a terminal to install nodemon into our project
directory.

> 여러분들이 보셨듯이 우리는 노 데몬을 사용하고 있습니다. 노 데몬은 우리의 서버를 디버그 모드로 시작하고 파일들의 변경사항들을 관찰할 수 있습니다.
> 그리고 서버를 재시작합니다. 프로젝트 디렉토리로 nodemon을 설치하기 위해서 터미널에서 다음 명령어를 실행하세요.

```shell
$ npm install nodemon
```

Let’s start our application and confirm that it is running properly.

> 애플리케이션을 시작하고 제대로 동작하는지 확인해보세요.

```ignore
$ docker compose -f docker-compose.dev.yml up --build
```

We pass the `--build` flag so Docker compiles our image and then starts it.

> 우리는 도커가 우리의 이미지를 빌드하고 나서 시작할 수 있도록 `--build` 옵션을 전달하였습니다.

If all goes well, you should see something similar:

> 모든것이 잘되면, 여러분들은 다음과 같은 결과를 볼 것입니다.

![](https://docs.docker.com/language/nodejs/images/node-compile.png)

Now let’s test our API endpoint. Run the following curl command:

> API를 테스트해봅시다. 다음과 같은 curl 명령어를 실행해보세요.

```shell
$ curl --request GET --url http://localhost:8000/notes
```

You should receive the following response:

> 여러분들은 다음과 같은 응답을 받습니다.

```json
{"code":"success","meta":{"total":0,"count":0},"payload":[]}
```

### 디버거에 연결하기(Connect a debugger)

We’ll use the debugger that comes with the Chrome browser. Open Chrome on your machine and then type the following into
the address bar.

> 우리는 크롬 브라우저를 가지고 디버거를 사용할 것입니다.
> 머신에 크롬을 열고나서 주소창에서 다음과 같이 입력하세요.

```
about:inspect
```

It opens the following screen.

> 다음과 같은 화면이 열립니다.

![](https://docs.docker.com/language/nodejs/images/chrome-inspect.png)

Select **Configure**. This opens the **Target discovery settings**. Specify the target `127.0.0.1:9229` if it does not
exist and
then select **Done**.

> Configure를 선택하세요. **Target discovery settings**가 열립니다. 만약 target `127.0.0.1:9229`를 명세하세요. 그런다음 Done을 선택하세요.

Select the **Open dedicated DevTools for Node** link. This opens the DevTools that are connected to the running Node.js
process inside our container.

> **Open deicated DevTools for Node** 링크를 선택하세요. 컨테이너 안에서 실행중인 Node.js 프로세스에 연결된 DevTools가 열립니다.

Let’s change the source code and then set a breakpoint.

> 소스 코드를 변경하고 나서 breakpoint를 설정하세요.

Add the following code above the existing `server.use()` statement, and save the file. Make sure that the return
statement
is on a line of its own, as shown here, so you can set the breakpoint appropriately.

> `server.use()` 구문 위에 다음 코드를 추가하세요. 그리고 파일을 저장하세요. 브레이크포인트를 적절하게 설정할 수 있도록 return 문이 여기에 표시된대로 줄에 있는지 확인합니다.

```javascript
server.use( '/foo', (req, res) => {
   return res.json({ "foo": "bar" })
 })
```

If you take a look at the terminal where our Compose application is running, you’ll see that nodemon noticed the changes
and reloaded our application.

> 우리의 Compose 애플리케이션 실행 터미널을 살펴본다면, 우리는 재로드된 애플리케이션과 변경사항들을 nodemon이 알려주는 것을 볼 것입니다.

![](https://docs.docker.com/language/nodejs/images/nodemon.png)

Navigate back to the Chrome DevTools and set a breakpoint on the line containing the return res.json({ "foo": "bar" })
statement, and then run the following curl command to trigger the breakpoint.

> 크롬 개발툴로 돌아오고 res.json({ "foo": "bar" }) 구문을 반환하는 것을 포함하는 라인에서 브레이크포인트를 설정하세요.
> 그리고 나서 브레이크포인트를 트리거하기 위해서 다음 curl 명령어를 실행하세요.

```shell
$ curl --request GET --url http://localhost:8000/foo
```

You should have seen the code stop at the breakpoint and now you are able to use the debugger just like you would
normally. You can inspect and watch variables, set conditional breakpoints, view stack traces, etc.

> 여러분들은 브레이크포인트에서 코드가 정지 된것을 볼것입니다. 그리고 여러분들은 디커거를 사용할 수 있습니다.
> 여러분들은 변수들을 검사하고 볼 수 있습니다. 조건부적인 브레이크포인트를 설정하고, 스택 트레이스를 보기 등을 할 수 있습니다.

### 다음 단계(Next steps)

In this module, we took a look at creating a general development image that we can use pretty much like our normal
command line. We also set up our Compose file to map our source code into the running container and exposed the
debugging port.

> 이번장에서 우리는 일반적인 일반적인 명령어 라인과 같이 사용할 수 있는 개발 이미지 생성하는 것을 봤습니다.
> 우리는 또한 소스코드에서 실행중인 컨테이너로 매핑하기 위해서 Compose 파일을 설정하였습니다.
> 그리고 디버깅 포트를 노출시켰습니다.

In the next module, we’ll take a look at how to run unit tests in Docker. See:

> 다음 장에서는 우리는 도커에서 단위 테스트를 실행하는 방법에 대해서 배울 것입니다.


