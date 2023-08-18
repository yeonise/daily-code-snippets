## Build your Node image

### 선행조건(Prerequisites)

- You understand basic [Docker concepts](https://docs.docker.com/get-started/overview/).
- You’re familiar with the [Dockerfile format](https://docs.docker.com/build/building/packaging/#dockerfile).
- You have [enabled BuildKit](https://docs.docker.com/build/buildkit/#getting-started) on your machine.

> - 기본적인 도커 개념
> - Dockerfile 형식에 익숙해지기
> - 머신에 빌드 킷 활성화하기

### 개요(Overview)

Now that we have a good overview of containers and the Docker platform, let’s take a look at building our first image.
An image includes everything you need to run an application - the code or binary, runtime, dependencies, and any other
file system objects required.

> 우리는 컨테이너와 도커 플랫폼에 대한 좋은 개요를 가지고 있습니다.
> 우선 첫번째로 이미지를 빌드하는 것부터 살펴보겠습니다.
> 이미지는 애플리케이션을 실행하기 위해 필요한 모든것이 포함된 것입니다.

To complete this tutorial, you need the following:

> 튜토리얼을 완료하기 위해서는 다음과 같은 것들이 필요합니다.

- Node.js version 18 or later. Download Node.js
- Docker running locally: Follow the instructions to [download and install Docker](https://docs.docker.com/desktop/).
- An IDE or a text editor to edit files. We recommend using Visual Studio Code.

> - Node.js 18 버전 이상을 다운로드
> - 도커를 지역적으로 실행하는것: 도커를 다운로드하고 설치하기 위해서 다음 링크를 참조해주세요.
> - 파일을 편지하기 위한 IDE 또는 텍스트 편집기. 우리는 Visual Studio Code를 추천합니다.

### 샘플 애플리케이션(Sample application)

Let’s create a simple Node.js application that we can use as our example. Create a directory in your local machine named
node-docker and follow the steps below to create a simple REST API.

> 간단한 Node.js 애플리케이션을 생성해봅시다.
> 로컬 머신에 node-docker라는 이름으로 디렉토리를 생성하세요.
> 그리고 간단한 REST API를 생성하기 위해서 아래 단계들을 따라하세요.

```shell
$ cd [path to your node-docker directory]
$ npm init -y
$ npm install ronin-server ronin-mocks
$ touch server.js
```

Now, let’s add some code to handle our REST requests. We’ll use a mock server so we can focus on Dockerizing the
application.

> REST 요청들을 다루기 위해서 몇몇 코드를 추가해봅시다.
> 우리는 목 서버(mock server)를 사용할 것입니다.
> 그래서 우리는 애플리케이션을 도커화하는데 집중할 수 있습니다.

Open this working directory in your IDE and add the following code into the server.js file.

> IDE에 워킹 디렉토리를 열고 server.js 파일에 다음 코드를 입력하세요.

```javascript
const ronin = require('ronin-server')
const mocks = require('ronin-mocks')

const server = ronin.server()

server.use('/', mocks.server(server.Router(), false, true))
server.start()
```

The mocking server is called Ronin.js and will listen on port 8000 by default. You can make POST requests to the root (
/) endpoint and any JSON structure you send to the server will be saved in memory. You can also send GET requests to the
same endpoint and receive an array of JSON objects that you have previously POSTed.

> 목 서버는 Ronin.js라고 불리고 기본적으로 8080 포트로 열려있습니다.
> 여러분들은 root(/) 경로로 POST 요청을 할 수 있고 서버로 전송하는 모든 JSON 구조가 메모리에 저장됩니다.
> 여러분들은 또한 같은 경로로 GET 요청을 보낼 수 있습니다. 그리고 이전에 보낸 POST에 대한 JSON 객체의 배열을 받을 수 있습니다.

### 애플리케이션 테스트(Test the application)

Let’s start our application and make sure it’s running properly. Open your terminal and navigate to your working
directory you created.

> 애플리케이션을 시작하고 제대로 동작하는지 확인해봅시다.
> 여러분들의 터미널을 열고 여러분들이 생성한 워킹 디렉토리로 이동합니다.

```shell
$ node server.js
```

To test that the application is working properly, we’ll first POST some JSON to the API and then make a GET request to
see that the data has been saved. Open a new terminal and run the following curl commands:

> 애플리케이션이 제대로 동작하는지 테스트하기 위해서 우리는 첫번째로 API에 POST 요청을 날리고 데이터가 잘 저장되었는지 GET 요청을 날릴 것입니다.
> 새로운 터미널을 열고 다음과 같이 curl 명령어들을 수행하세요.

```shell
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing" }'
{"code":"success","payload":[{"msg":"testing","id":"31f23305-f5d0-4b4f-a16f-6f4c8ec93cf1","createDate":"2020-08-28T21:53:07.157Z"}]}

$ curl http://localhost:8000/test
{"code":"success","meta":{"total":1,"count":1},"payload":[{"msg":"testing","id":"31f23305-f5d0-4b4f-a16f-6f4c8ec93cf1","createDate":"2020-08-28T21:53:07.157Z"}]}
```

Switch back to the terminal where our server is running. You should now see the following requests in the server logs.

> 서버가 실행중인 터미널로 다시 올아옵니다.
> 여러분들은 서버 로그에 다음처럼 요청 기록들을 볼 수 있습니다.

```shell
2020-XX-31T16:35:08:4260  INFO: POST /test
2020-XX-31T16:35:21:3560  INFO: GET /test
```

Great! We verified that the application works. At this stage, you’ve completed testing the server script locally.

> 좋습니다! 우리는 애플리케이션이 동작하는 것을 확인하였습니다. 이번 단계에서는 여러분들은 서버 스크립트가 지역적으로 테스트하는 것을 완료하였습니다.

Press CTRL-c from within the terminal session where the server is running to stop it.

> CTRL - c를 눌러 터미널 세션에서 나오고 서버를 중지하세요.

```shell
2021-08-06T12:11:33:8930  INFO: POST /test
2021-08-06T12:11:41:5860  INFO: GET /test
^Cshutting down...
```

We will now continue to build and run the application in Docker.

> 우리는 도커에서 애플리케이션을 빌드하고 실행하는 것을 계속할 것입니다.

### Node.js에 대한 Dockerfile 생성하기(Create a Dockerfile for Node.js)

Next, we need to add a line in our Dockerfile that tells Docker what base image we would like to use for our
application.

> 다음으로 우리는 Dockerfile에 다음 줄을 추가해야 합니다.
> 추가하는 줄의 의미는 도커가 애플리케이션을 사용하기 위해서 어떤 이미지를 기반으로 할지 알려주는 것입니다.

```
# syntax=docker/dockerfile:1

FROM node:18-alpine
```

Docker images can be inherited from other images. Therefore, instead of creating our own base image, we’ll use the
official Node.js image that already has all the tools and packages that we need to run a Node.js application. You can
think of this in the same way you would think about class inheritance in object oriented programming. For example, if we
were able to create Docker images in JavaScript, we might write something like the following.

> 도커 이미지는 다른 이미지들로부터 상속받을 수 있습니다. 그러므로 베이스 이미지를 생성하는 것 대신에 우리는 Node.js 애플리케이션을 실행하기 위해 필요한
> 모든 도구와 패키지들이 준비된 공식적인 Node.js 이미지를 사용할 수 있습니다.
> 여러분들은 객체 지향 프로그래밍에서 클래스 상속에 대해 생각하는 것과 같은 방식으로 생각할 수 있습니다.
> 예를 들어 만약 우리가 자바스크립트로 도커 이미지를 만들 수 있다면 다음과 같은 것들을 작성할 수 있습니다.

```js
class MyImage extends NodeBaseImage {}
```

This would create a class called `MyImage` that inherited functionality from the base class `NodeBaseImage`.

> 이것은 NodeBaseImage 베이스 클래스로부터 기능을 상속받은 MyImage 클래스를 생성한 것입니다.

In the same way, when we use the FROM command, we tell Docker to include in our image all the functionality from the
node:18-alpine image.

> 같은 방법으로 우리가 FROM 명령어를 사용할때 도커에게 node:18-alpine 이미지로부터 모든 기능들을 우리의 이미지에 포함하라고 말하는 것입니다.

Note

If you want to learn more about creating your own base images,
see [Creating base images](https://docs.docker.com/build/building/base-images/).

> 참고
>
> 만약 여러분이 베이스 이미지들을 만드는 것을 배우고 싶다면 베이스 이미지 생성하기 링크를 참고해주세요.


The `NODE_ENV` environment variable specifies the environment in which an application is running (usually, development
or
production). One of the simplest things you can do to improve performance is to set `NODE_ENV` to `production`.

> NODE_ENV 환경 변수는 애플리케이션이 실행할때 (보통, 개발 또는 프로덕션 환경) 필요한 변수들을 명세합니다.
> 여러분들이 성능을 개선할 수 있는 가장 간단한 것은 `NODE_ENV`를 `production`으로 설정하는 것입니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM node:18-alpine

ENV NODE_ENV=production
```

To make things easier when running the rest of our commands, let’s create a working directory. This instructs Docker to
use this path as the default location for all subsequent commands. This way we do not have to type out full file paths
but can use relative paths based on the working directory.

> 나머지 명령어들을 쉽게 실행할 수 있도록 하기 위해서 작업 디렉토리를 생성해봅시다.
> 이 명령어는 도커에게 모든 서브 시퀀스 명령어들에 대하여 기본적인 위치로서 경로를 지정하는 명령어입니다.
> 이렇게 하면 전체 파일 경로를 입력할 필요가 없고 작업 디렉토리에 기반한 상대 경로를 사용할 수 있습니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM node:18-alpine

ENV NODE_ENV=production
WORKDIR /app
```

Usually the very first thing you do once you’ve downloaded a project written in Node.js is to install npm packages. This
ensures that your application has all its dependencies installed into the `node_modules` directory where the Node
runtime
will be able to find them.

> 보통 Node.js로 작성된 프로젝트를 다운받으면 첫번째로 npm 패키지를 설치합니다.
> npm 패키지를 설치하게 되면 애플리케이션이 Node 런타임에서 찾을 수 있는 node_modules 디렉토리에 모든 종속성을 설치할 수 있습니다.

Before we can run `npm install`, we need to get our package.json and package-lock.json files into our images. We use the
`COPY` command to do this. The `COPY` command takes two parameters: `src` and `dest`. The first parameter `src` tells
Docker what
file(s) you would like to copy into the image. The second parameter `dest` tells Docker where you want that file(s) to
be
copied to. For example:

> `npm install`을 수행하기전에 우리는 package.json과 package-lock.json 파일을 우리의 이미지로 넣을 필요가 있습니다.
> 우리는 COPY 명령어를 사용하여 할 수 있습니다.
> COPY 명령어는 두개의 파라미터인 `src`와 `dest`를 가집니다.
> 첫번째 파라미터 `src`는 여러분이 이미지로 복사하고자 하는 파일을 도커에게 알려줍니다.
> 두번째 파라미터는 여러분이 목적지를 도커에게 알려줍니다.

```
COPY ["<src>", "<dest>"]
```

You can specify multiple src resources seperated by a comma. For example, `COPY ["<src1>", "<src2>",..., "<dest>"]`.
We’ll
copy the `package.json` and the `package-lock.json` file into our working directory `/app`.

> 여러분들은 콤마로 구분하여 여러개의 src 리소스들을 명세할 수 있습니다. 예를 들어 COPY ["<src1>", "<src2>",..., "<dest>"]와 같이 할수 있습니다.
> 우리는 package.json과 package-lock.json 파일을 워킹 디렉리의 /app으로 복사할 것입니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM node:18-alpine

ENV NODE_ENV=production
WORKDIR /app
COPY ["package.json", "package-lock.json*", "./"]
```

Note that, rather than copying the entire working directory, we are only copying the package.json file. This allows us
to take advantage of cached Docker layers. Once we have our files inside the image, we can use the `RUN` command to
execute the command npm install. This works exactly the same as if we were running npm install locally on our machine,
but this time these Node modules will be installed into the `node_modules` directory inside our image.

> 전체 작업 데릭토리를 복사하는 것이 아닌 package.json 파일만 복사합니다.
> 이것은 우리에게 캐싱된 도커 레이어의 장점을 취할 수 있습니다.
> 일단 우리가 우리의 파일들을 이미지 안으로 복사하게 되면, 우리는 npm install 명령어를 실행하기 위하여 RUN 명령어를 사용할 수 있습니다.
> 이것은 우리가 머신에서 지역적으로 npm install을 실행하는 것과 동일하지만, 이번에는 이러한 Node modules이 이미지 내의 node_modules 디렉토리에 설치될 것입니다.

```dockerfile
# syntax=docker/dockerfile:1

FROM node:18-alpine

ENV NODE_ENV=production
WORKDIR /app
COPY ["package.json", "package-lock.json*", "./"]
RUN npm install --production
```

At this point, we have an image that is based on node version 18 and we have installed our dependencies. The next thing
we need to do is to add our source code into the image. We’ll use the COPY command just like we did with our
package.json files above.

> 이 시점에서 우리는 node 18버전 기반과 의존성이 설치된 이미지를 가지게 됩니다.
> 다음으로 생각할 것은 소스코드를 이미지에 추가하는 것입니다. 우리는 위의 package.json 파일 처럼 COPY 명령어를 사용할 것입니다.

```
# syntax=docker/dockerfile:1

FROM node:18-alpine

ENV NODE_ENV=production
WORKDIR /app
COPY ["package.json", "package-lock.json*", "./"]
RUN npm install --production
COPY . .
```

The COPY command takes all the files located in the current directory and copies them into the image. Now, all we have
to do is to tell Docker what command we want to run when our image is run inside of a container. We do this with
the `CMD`
command.

> COPY 명령어는 현재 디렉토리에 있는 모든 파일들을 이미지로 복사 및 붙여넣기 합니다.
> 이제 이미지가 컨테이너 내부에서 실행 될때 실행할 명령어를 도커에게 알려주기만 하면 됩니다. 이 작업은 `CMD` 명령을 사용하여 수행합니다.

```
# syntax=docker/dockerfile:1

FROM node:18-alpine

ENV NODE_ENV=production
WORKDIR /app
COPY ["package.json", "package-lock.json*", "./"]
RUN npm install --production
COPY . .
CMD ["node", "server.js"]
```

Here’s the complete Dockerfile.

> 완성된 Dockerfile입니다.

```
# syntax=docker/dockerfile:1

FROM node:18-alpine
ENV NODE_ENV=production

WORKDIR /app

COPY ["package.json", "package-lock.json*", "./"]

RUN npm install --production

COPY . .

CMD ["node", "server.js"]
```

### .dockerignore 파일 생성하기(Create a .dockerignore file)

To use a file in the `build context`, the Dockerfile refers to the file specified in an instruction, for example, a COPY
instruction. A `.dockerignore` file lets you specify files and directories to be excluded from the build context. To
improve the build’s performance, create a `.dockerignore` file and add the `node_modules` directory in it:

> `build context`에서 파일을 사용하기 위해서 Dockerfile은 명령어(예, COPY 명령어)에 지정된 파일을 참조합니다.
> `.dockerignore` 파일은 여러분이 명세한 파일들과 디렉토리를 빌드 컨텍스트에서 제외합니다.
> 빌드의 성능을 개선하기 위해서 `.dockerignore` 파일을 생성하고 거기에 `node_modules` 디렉토리를 추가하세요.

```ignore
node_modules
```

### 이미지 빌드하기(Build image)

Now that we’ve created our Dockerfile, let’s build our image. To do this, we use the `docker build` command. The docker
build command builds Docker images from a Dockerfile and a “context”. A build’s context is the set of files located in
the specified PATH or URL. The Docker build process can access any of the files located in the context.

> 우리는 Dockerfile을 생성하였습니다. 이제 이미지를 빌드해봅시다. 그러기 위해서는 우리는 `docker build` 명령어를 사용합니다.
> `docker build` 명령어는 Dockerfile과 "context"로부터 도커 이미지를 빌드합니다.
> 빌드의 컨텍스트는 PATH 또는 URL에 위치한 파일들의 모음입니다.
> 도커 빌드 프로세스는 컨텍스트에 있는 모든 파일들에 접근이 가능합니다.

The build command optionally takes a `--tag` flag. The tag is used to set the name of the image and an optional tag in
the
format `‘name:tag’`. We’ll leave off the optional “tag” for now to help simplify things. If you do not pass a tag,
Docker
will use “latest” as its default tag. You’ll see this in the last line of the build output.

> 빌드 명령어는 선택적으로 `--tag` 옵션을 사용할 수 있습니다.
> `--tag`는 이미지의 이름을 설정하는데 사용되고 선택적인 옵션입니다. 형식은 `'name:tag'`입니다.
> 우리는 간단하게 하기 위해서 "tag" 옵션을 뺄것입니다. 만약 여러문이 태그를 전달하지 않는다면 도커는 기본적으로 "latest" 태그를 사용할 것입니다.
> 여러분들은 빌드 출력의 마지막 줄에 태그 결과를 볼것입니다.

Let’s build our first Docker image.

> 우리의 첫번째 도커 이미지를 빌드해봅시다.

```
$ docker build --tag node-docker .

[+] Building 93.8s (11/11) FINISHED
 => [internal] load build definition from dockerfile                                          0.1s
 => => transferring dockerfile: 617B                                                          0.0s
 => [internal] load .dockerignore                                                             0.0s
 ...
 => [2/5] WORKDIR /app                                                                        0.4s
 => [3/5] COPY [package.json, package-lock.json*, ./]                                         0.2s
 => [4/5] RUN npm install --production                                                        9.8s
 => [5/5] COPY . .
```

### 로컬 이미지 보기(View local images)

To see a list of images we have on our local machine, we have two options. One is to use the CLI and the other is to use
Docker Desktop. Since we are currently working in the terminal let’s take a look at listing images with the CLI.

> 로컬 머신이 가지고 있는 이미지들의 목록을 보기 위해서 두가지 옵션이 있습니다. 하나는 CLI를 사용하는 것이고 다른 방법은 도커 데스크톱을 사용하는 것입니다.
> 우리는 현재 터미널에서 작업중이기 때문에 CLI을 가지고 이미지의 목록을 봅시다.

To list images, simply run the `images` command.

> 이미지 목록들을 보기 위해서 간단하게 `images` 명령어를 실행합니다.

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
node-docker         latest              3809733582bc        About a minute ago   945MB
```

Your exact output may vary, but you should see the image we just built node-docker:latest with the latest tag.

> 우리의 예상 출력이 다를수 있습니다. 그러나 여러분들은 node-docker:latest 이미지를 볼것입니다.

### 이미지 태그하기(Tag images)

An image name is made up of slash-separated name components. Name components may contain lowercase letters, digits and
separators. A separator is defined as a period, one or two underscores, or one or more dashes. A name component may not
start or end with a separator.

> 이미지 이름은 슬래시로 구분된 이름 구성요소로 만들어집니다.
> 이름 구성요소들은 소문자, 숫자, 구분자를 포함할 수 있습니다.
> 구분자는 기간, 한두개의 밑줄, 한개이상의 슬래시입니다.
> 이름 구성요소는 구분자로 시작하거나 종료할 수 없습니다.

An image is made up of a manifest and a list of layers. In simple terms, a “tag” points to a combination of these
artifacts. You can have multiple tags for an image. Let’s create a second tag for the image we built and take a look at
its layers.

> 이미지는 레이어들의 목록과 manifest로 이루어져 있습니다. 간단하게 태그는 이러한 아티팩트들의 조합을 나타냅니다.
> 여러분들은 한 이미지에 대하여 여러개의 태그를 가질 수 있습니다.
> 우리가 빌드한 이미지에 대하여 두번째 태그를 만들어봅시다.

To create a new tag for the image we built above, run the following command.

> 우리가 빌드한 이미지에 대하여 새로운 태그를 생성하기 위하여 다음 명령어를 실행합니다.

```shell
$ docker tag node-docker:latest node-docker:v1.0.0
```

The Docker tag command creates a new tag for an image. It does not create a new image. The tag points to the same image
and is just another way to reference the image.

> 도커 tag 명령어는 이미지에 대하여 새로운 태그를 생성합니다. 태그는 새로운 이미지를 생성하지 않습니다.
> 태그는 같은 이미지를 가리킵니다. 그리고 이미지를 참조하기 위한 다른 방법입니다.

Now run the `docker images` command to see a list of our local images.

> 지역 이미지들의 목록을 보기 위하여 `docker images` 명령어를 실행하세요.

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node-docker         latest              3809733582bc        24 minutes ago      945MB
node-docker         v1.0.0              3809733582bc        24 minutes ago      945MB
```

You can see that we have two images that start with node-docker. We know they are the same image because if you look at
the IMAGE ID column, you can see that the values are the same for the two images.

> 여러분들은 우리가 두개의 이미지를 가지고 있는 것을 볼 수 있습니다.
> 우리는 2개의 이미지가 같은 이미지인지 압니다. 왜냐하면 IMAGE ID 컬럼을 볼때 두개의 이미지들에 대하여 같은 IMAGE ID 값을 가지는 것을 볼 수 있습니다.

Let’s remove the tag that we just created. To do this, we’ll use the `rmi` command. The `rmi` command stands for “remove
image”.

> 우리가 방금 생성한 태그를 삭제해봅시다. 그러기 위해서는 우리는 `rmi` 명령어를 사용해야 합니다.
> rmi 명령어는 이미지를 삭제하는 명령어입니다.

```shell
$ docker rmi node-docker:v1.0.0
Untagged: node-docker:v1.0.0
```

Notice that the response from Docker tells us that the image has not been removed but only “untagged”. Verify this by
running the `images` command.

> 도커로부터의 응답은 위리에게 이미지가 삭제되지 않았다는 것을 알려줍니다. 그러나 오직 "untagged" 되었다는 것을 알려줍니다.
> `images` 명령어를 실행하여 확인하세요.

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node-docker         latest              3809733582bc        32 minutes ago      945MB
```

Our image that was tagged with `:v1.0.0` has been removed but we still have the `node-docker:latest` tag available on
our
machine.

> `:v1.0.0` 태그를 가진 이미지가 삭제되었습니다. 그러나 우리는 여전히 `node-docker:latest` 태그를 가지고 있습니다.

### 다음 단계(Next steps)

In this module, we took a look at setting up our example Node application that we will use for the rest of the tutorial.
We also created a Dockerfile that we used to build our Docker image. Then, we took a look at tagging our images and
removing images. In the next module, we’ll take a look at how to:

> 이번 모듈에서는 우리는 Node 애플리케이션을 세팅하는 것을 보았습니다. 우리는 이번 모듈에서 생성한 애플리케이션을 나머지 튜토리얼에서도 사용할 것입니다.
> 우리는 또한 Dockerfile을 생성하였습니다. Dockerfile은 도커 이미지를 빌드하기 위해 사용됩니다. 그러고 나서 우리는 이미지에 태그하는 것을 보았고
> 이미지를 삭제하는 것에 대해서 알아보았습니다. 다음 모듈에서는 우리는 이미지를 컨테이너로 시작하는 것에 대해서 배울 것입니다.

