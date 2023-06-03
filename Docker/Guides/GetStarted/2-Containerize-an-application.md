## Containerize an application

For the rest of this guide, you’ll be working with a simple todo list manager that runs on Node.js.
If you’re not familiar with Node.js, don’t worry.
This guide doesn’t require any prior experience with JavaScript.

> 이 가이드의 나머지 부분에서는 Node.js 에서 실행되는 간단한 TodoList 매니저와 함께 작업합니다.
> 만약 여러분들이 Node.js에 익숙하지 않아도 괜찮습니다.
> 이 가이드는 자바스크립트에 대한 어떤 경험도 요구하지 않습니다.

To complete this guide, you’ll need the following:

> 이 가이드를 완료하기 위해서는 여러분들은 다음과 같은 것이 필요합니다.

- Docker running locally. Follow the instructions to download and install Docker.
- A Git client.
- An IDE or a text editor to edit files. Docker recommends using Visual Studio Code.
- A conceptual understanding of containers and images.

> - 로컬에서 실행되는 도커. 설명서에 따라서 도커를 다운로드하고 설치합니다.
> - 깃(Git) 클라이언트
> - 파일들을 편집하기 위한 텍스트 편집기 또는 통합개발환경(IDE). 도커는 비주얼 스튜디오 코드를 사용하는 것을 추천합니다.
> - 컨테이너와 이미지의 개념 이해

### 앱 가져오기(Get the app)

Before you can run the application, you need to get the application source code onto your machine.

> 여러분들이 애플리케이션을 실행하기전에, 여러분들은 여러분들의 컴퓨터에 애플리케이션 소스 코드를 가져오는 것이 필요합니다.

1. Clone the getting-started repository using the following command:

> 다음 명령을 사용하여 getting-started 저장소를 복사합니다.

```bash
$ git clone https://github.com/docker/getting-started.git
```

2. View the contents of the cloned repository. Inside the getting-started/app directory you should
   see package.json and two subdirectories (src and spec).

> 복사된 저장소의 내용을 봐주세요. getting-started/app 디렉토리 안에 package.json과 src, spec 디렉토리가 있습니다.

### 앱의 컨테이너 이미지를 빌드하기(Build the app’s container image)

To build the container image, you’ll need to use a Dockerfile. A Dockerfile is simply a text-based
file with no file extension that contains a script of instructions. Docker uses this script to build
a container image.

> 컨테이너 이미지를 빌드하기 위해서는 Dockerfile이 필요합니다. Dockerfile은 확장자가 없고 명령어들의 스크립트가 포함된
> 간단한 텍스트 기반 파일입니다. 도커는 Dockerfile을 사용하여 컨테이너 이미지를 빌드합니다.

> 즉, Dockerfile은 도커 이미지를 만들기 위해 필요한 명령어들을 작성한 스크립트 파일입니다.

1. In the app directory, the same location as the package.json file, create a file named Dockerfile.
   You can use the following commands below to create a Dockerfile based on your operating system.

> app 디렉토리 안에, 같은 위치에 있는 package.json 파일 위치에 Dockerfile 이름으로 파일을 생성합니다.
> 여러분들은 다음과 같은 명령어로 아래와 같이 Dockerfile을 생성할 수 있습니다.

**Mac/Linux**

In the terminal, run the following commands listed below.

> 터미널 안에서 아래와 같이 명령어를 실행시킵니다.

Change directory to the app directory. Replace /path/to/app with the path to your
getting-started/app directory.

> app 디렉토리로 디렉토리를 변경합니다. 여러분들의 getting-started/app 디렉토리로 경로를 /path/to/app으로 대신합니다.

```shell
$ cd \path\to\app
=>
$ cd .\getting-started\app
```

Create an empty file named Dockerfile.

> Dockerfile 이라는 이름의 빈 파일을 생성합니다.

```shell
$ touch Dockerfile
```

**Windows**

In the Windows Command Prompt, run the following commands listed below.

> 윈도우 명령어 프롬프트에서 다음 명령어들을 실행합니다.

Change directory to the app directory. Replace \path\to\app with the path to your
getting-started\app directory.

> app 디렉토리로 디렉토리를 변경합니다. 여러분들의 getting-started/app 디렉토리로 경로를 /path/to/app으로 대신합니다.

```shell
$ cd \path\to\app
=>
$ cd .\getting-started\app
```

Create an empty file named Dockerfile.

> Dockerfile 이라는 이름의 파일을 생성합니다.

```shell
$ type nul > Dockerfile
```

2. Using a text editor or code editor, add the following contents to the Dockerfile:

> 텍스트 편집기 또는 코드 편집기를 사용하여 Dockerfile에 다음 내용들을 추가합니다.

```docker
# syntax=docker/dockerfile:1
   
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

- `syntax=docker/dockerfile:1`
    - 도커 빌더에게 도커 파일을 파싱할때 어떤 문법으로 파싱해야 하는지 알려주는 역할입니다.
- `FROM node:18-alpine`
    - node.js 18-alpine 버전의 이미지를 사용합니다.
    - alpine은 가장 작은 사이즈의 정말 필요한 것들만 담겨 있는 이미지를 의미합니다.
- `WORKDIR /app`
    - 작업 디렉토리를 /app로 설정합니다.
    - 도커 컨테이너 내에서 애플리케이션 코드를 저장하는데 사용됩니다.
- `COPY . .`
    - 현재 Dockerfile이 위치한 디렉토리의 모든 파일과 폴더를 `/app` 디렉토리로 복사합니다.
    - 애플레케이션 코드 및 종속성을 도커 컨테이너 내부로 복사하여 이미지에 포함시키는 역할을 수행합니다.
- `RUN yarn install --production`
    - 애플리케이션의 종속성을 설치합니다.
    - `--production` 플래그는 개발용 종속성을 설치하지 않고, 프로덕션 환경에서 필요한 종속성만 설치하도록 합니다.
- `CMD ["node", "src/index.js"]`
    - 컨테이너가 실행될 때 실행되는 기본 명령을 설정합니다.
    - `node src/index.js` 명령어이 실행되어 Node.js 애플리케이션을 시작합니다.
- `EXPOST 3000`
    - 컨테이너가 외부와 통신할 수 있는 포트를 노출합니다.
    - `3000` 포트가 노출되어 애플리케이션의 네트워크 트래픽을 처리할 수 있도록 합니다.

3. Build the container image using the following commands:

> 다음 명령어를 사용하여 컨테이너 이미지를 빌드합니다.

In the terminal, change directory to the getting-started/app directory. Replace /path/to/app with the path to your
getting-started/app directory.

> 터미널에서 getting-started/app 디렉토리로 현재 디렉토리를 변경합니다.
> `/path/to/app` 명령어를 여러분들의 getting-started/app 디렉토리 경로로 변경하세요.

```shell
$ cd /path/to/app
=>
$ cd ./getting-started/app
```

Build the container image.

> 컨테이너 이미지를 빌드합니다.

```shell
$ docker build -t getting-started .
```

The docker build command uses the Dockerfile to build a new container image. You might have noticed that Docker
downloaded a lot of “layers”. This is because you instructed the builder that you wanted to start from the node:
18-alpine image. But, since you didn’t have that on your machine, Docker needed to download the image.

> 새로운 컨테이너 이미지를 빌드하기 위해서 도커 빌드 명령어는 Dockerfile을 사용합니다. 여러분들은 도커가 많은 레이어들을 다운로드한 것을
> 알림 받을 수 있습니다.
> 이것은 여러분들이 `node:18-alpine`에서 시작하는 빌드를 지시했기 때문입니다.
> 그러나, 여러분들은 여러분들의 컴퓨터에 가지고 있지 않기 때문에 도커는 이미지를 다운로드해야 했습니다.

After Docker downloaded the image, the instructions from the Dockerfile copied in your application and used yarn to
install your application’s dependencies. The CMD directive specifies the default command to run when starting a
container from this image.

> 도커가 이미지를 다운로드한 후에, Dockerfile에 있는 명령어들은 여러분들의 애플리케이션에 복사되고 여러분들의 애플리케이션의 종속성들을
> 설치하기 위해서 yarn 명령어를 사용합니다.
> CMD 명령어는 이 이미지에서 컨테이너를 시작할 때 실행할 기본 명령어를 지정합니다. 즉, 컨테이너가 생성되자마자 애플리케이션이 실행됩니다.

Finally, the -t flag tags your image. Think of this simply as a human-readable name for the final image. Since you named
the image getting-started, you can refer to that image when you run a container.

> 마지막으로 -t 플래그는 이미지에 태그를 지정합니다. 이것은 단순히 사람이 읽을 수 있는 최종 이미지에 대한 이름입니다.
> 여러분들은 이미지에 getting-started라는 이름으로 지었기 때문에, 여러분들이 컨테이너를 실행할 때 이미지에 태그 이름을 참조하여 지정할 수 있습니다.

The . at the end of the docker build command tells Docker that it should look for the Dockerfile in the current
directory.

> 도커 빌드 명령어 마지막에 `.`은 도커에게 현재 디렉토리에 존재하는 Dockerfile 파일을 참조하라고 알려주는 것입니다.

### 앱 컨테이너 실행하기(Start an app container🔗)

Now that you have an image, you can run the application in a container. To do so, you will use the `docker run` command.

> 지금 여러분들은 이미지를 가지고 있습니다. 여러분들은 컨테이너에서 애플리케이션을 실행할 수 있습니다. 그러기 위해서 여러분들은 `docker run` 명령어를 사용할 것입니다.

1. Start your container using the docker run command and specify the name of the image you just created:

> `docker run` 명령어를 사용하여 여러분들의 컨테이너를 시작하고 여러분들이 컨테이너 생성시 이미지의 이름을 명세합니다.

```shell
$ docker run -dp 3000:3000 getting-started
```

You use the -d flag to run the new container in “detached” mode (in the background). You also use the -p flag to create
a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, you wouldn’t be able to
access the application.

> `-d` 옵션을 사용하여 새로운 컨테이너를 "분리" 모드 (백그라운드)로 실행합니다.
> `-p` 옵션을 사용하여 호스트의 포트 3000번과 컨테이너의 포트 3000번을 설정합니다. 즉, 호스트의 3000번 포트로 요청이 들어오면 컨테이너의 3000번 포트로 이동됩니다.
> 포트 매핑없이 애플리케이션에 접근하는 것은 불가능합니다.

2. After a few seconds, open your web browser to `http://localhost:3000`. You should see your app.

> 몇 초후에, 여러분들의 웹 브라우저를 열고 `http://localhost:3000`으로 접속해보세요. 여러분들은 애플리케이션을 볼수 있을 것입니다.

![](https://docs.docker.com/get-started/images/todo-list-empty.png)

3. Go ahead and add an item or two and see that it works as you expect. You can mark items as complete and remove them.
   Your frontend is successfully storing items in the backend.

> 애플리케이션 한두가지 항목을 추가하여 예상대로 작동하는지 확인해보세요.
> 여러분들은 항목을 완료로 표시하고 제거할 수 있습니다. 애플리케이션의 프론트엔드가 백엔드에 항목을 성공적으로 저장할 것입니다.

At this point, you should have a running todo list manager with a few items, all built by you.
If you take a quick look at your containers, you should see at least one container running that is using the
getting-started image and on port 3000. To see your containers, you can use the CLI or Docker Desktop’s graphical
interface.

> 이때, 사용자가 작성한 몇가지 항목으로 작업 관리자를 실행해야 합니다.
> 만약 여러분들이 여러분들의 컨테이너들을 빠르게 살펴보자면, 여러분들은 최소 한개의 컨테이너가 수행되는 것을 볼 수 있습니다.
> 이 컨테이너는 포트 3000번에 getting-started 이미지를 사용하고 있을 것입니다.
> 여러분들의 컨테이너들을 보기 위해서는, 여러분들은 CLI 또는 도커 데스크탑의 GUI를 사용할 수 있습니다.

**CLI**

```
$ docker ps
```

**Docker Desktop**
In Docker Desktop, select the Containers tab to see a list of your containers.

> 도커 데스크톱에서, 여러분들의 컨테이너들의 리스르를 보기 위해서 컨테이너 탭을 선택하세요.

![](https://docs.docker.com/get-started/images/dashboard-two-containers.png)

### 다음 단계(Next steps)

In this short section, you learned the basics about creating a Dockerfile to build a container image. Once you built an
image, you started a container and saw the running app.

> 이번 짧은 부분에서는, 여러분들은 컨테이너 이미지를 빌드하기 위해서 Dockerfile을 생성하는 것에 대한 기본적인 것을 배웠습니다.
> 일단 여러분들이 이미지를 빌드하였다면, 여러분들은 컨테이너를 시작하고 실행해중인 애플리케이션을 볼 수 있었습니다.


