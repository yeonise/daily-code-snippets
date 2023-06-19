# Image-building best practices

### 이미지 레이어링(Image layering)

Did you know that you can look at what makes up an image? Using the docker image history command,
you can see the command that was used to create each layer within an image.

> 여러분은 이미지가 무엇으로 만들어졌는지 볼수 있다는 것을 아시나요?
> `docker image history` 명령어를 사용하여 이미지 내에 각각의 레이어를 생성하기 위해 사용된 명령어들을 볼 수 있습니다.

1. Use the `docker image history` command to see the layers in the `getting-started` image you
   created
   earlier in the tutorial.

> 여러분이 앞서 튜토리얼에서 생성한 `getting-started` 이미지 안에 레이어를 보기 위해서 `docker image history` 명렁어를 사용하세요.

```shell
$ docker image history getting-started
```

You should get output that looks something like this (dates/IDs may be different).

> 다음과 같은 결과(날짜, 아이디가 서로 다른)를 볼 것입니다.

```shell
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
 a78a40cbf866        18 seconds ago      /bin/sh -c #(nop)  CMD ["node" "src/index.j…    0B                  
 f1d1808565d6        19 seconds ago      /bin/sh -c yarn install --production            85.4MB              
 a2c054d14948        36 seconds ago      /bin/sh -c #(nop) COPY dir:5dc710ad87c789593…   198kB               
 9577ae713121        37 seconds ago      /bin/sh -c #(nop) WORKDIR /app                  0B                  
 b95baba1cfdb        13 days ago         /bin/sh -c #(nop)  CMD ["node"]                 0B                  
 <missing>           13 days ago         /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B                  
 <missing>           13 days ago         /bin/sh -c #(nop) COPY file:238737301d473041…   116B                
 <missing>           13 days ago         /bin/sh -c apk add --no-cache --virtual .bui…   5.35MB              
 <missing>           13 days ago         /bin/sh -c #(nop)  ENV YARN_VERSION=1.21.1      0B                  
 <missing>           13 days ago         /bin/sh -c addgroup -g 1000 node     && addu…   74.3MB              
 <missing>           13 days ago         /bin/sh -c #(nop)  ENV NODE_VERSION=12.14.1     0B                  
 <missing>           13 days ago         /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B                  
 <missing>           13 days ago         /bin/sh -c #(nop) ADD file:e69d441d729412d24…   5.59MB
```

Each of the lines represents a layer in the image. The display here shows the base at the bottom
with the newest layer at the top. Using this, you can also quickly see the size of each layer,
helping diagnose large images.

> 각각의 라인은 이미지의 하나의 레이어를 표현합니다.
> 위쪽이 최신 레이어이고 아래쪽이 베이스가 되는 레이어를 표시합니다.
> `docker image history` 명령어를 사용하여 각 레이어의 크키를 빠르게 확인할 수 있습니다.
> 각 레이어를 확인하여 큰 이미지를 진단하는데 도움이 됩니다.

2. You’ll notice that several of the lines are truncated. If you add the `--no-trunc` flag, you’ll
   get
   the full output.

> 여러분들은 라인의 여러줄이 잘린 것을 볼 수 있습니다.
> 만약 `--no-trunc` 옵션을 추가한다면 여러분들은 전체 결과를 볼 수 있습니다.

```yaml
$ docker image history --no-trunc getting-started
```

### 레이어 캐싱(Layer caching)

Now that you’ve seen the layering in action, there’s an important lesson to learn to help decrease
build times for your container images.

> 레이어가 작동하는 것을 봤으니 여러분들의 컨테이너 이미지들을 대상으로 빌드 시간을 줄이는데 도움이 되는 중요한 레슨이 있습니다.

Once a layer changes, all downstream layers have to be recreated as well

> 일단 레이어가 변겨오디면 모든 다운스트림 레이어들은 재 생성되어야 합니다.

Let’s look at the Dockerfile we were using one more time...

> 우리가 사용하던 Dockerfile을 봅시다.

```dockerfile
# syntax=docker/dockerfile:1
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```

Going back to the image history output, we see that each command in the Dockerfile becomes a new
layer in the image. You might remember that when we made a change to the image, the yarn
dependencies had to be reinstalled. Is there a way to fix this? It doesn’t make much sense to ship
around the same dependencies every time we build, right?

> 이미지 기록 결과로 다시 돌아가서 우리는 Dockerfile 안에 각각의 명령어들이 이미지에 새로운 레이어가 되는 것을 보았습니다.
> 여러분들은 우리가 이미지를 변경할 때, yarn 의존성들은 재 설치되어야 했습니다. 이 문제를 고칠 방법이 있을까요?
> 그것은 빌드할 때마다 동일한 의존성으로 전달하는 것은 그다지 말이 되지 않습니다. 그렇죠?

To fix this, we need to restructure our Dockerfile to help support the caching of the dependencies.
For Node-based applications, those dependencies are defined in the package.json file. So, what if we
copied only that file in first, install the dependencies, and then copy in everything else? Then, we
only recreate the yarn dependencies if there was a change to the package.json. Make sense?

> 이것을 고치기 위해서 우리는 의존성들의 캐싱을 지원하는 것을 도와주기 위해서 Dockerfile을 재구축이 필요합니다.
> Node 베이스 기반 애플리케이션에 대해서 이 의존성들은 package.json 파일에 정의되어 있습니다.
> 그래서 먼저 package.json 파일만 복사하고 의존성들을 설치한 다음 다른 모든 파일을 복사한다면 어떨까요?
> 그런 다음 우리는 package.json에 변경이 있을 경우에만 yarn 의존성을 참조하면 됩니다.

1. Update the Dockerfile to copy in the `package.json` first, install dependencies, and then copy
   everything else in.

> 먼저 package.json을 복사하는 명령어를 Dockerfile에 수정하세요. 의존성을 설치한다음에 다른 모든 항목들을 복사합니다.

```dockerfile
# syntax=docker/dockerfile:1
 FROM node:18-alpine
 WORKDIR /app
 COPY package.json yarn.lock ./
 RUN yarn install --production
 COPY . .
 CMD ["node", "src/index.js"]
```

2. Create a file named .dockerignore in the same folder as the Dockerfile with the following
   contents.

> 같은 디렉토리 위치에 `.dockerignore` 파일을 생성하세요.
> .dockerignore 파일에 다음 내용을 작성하세요.

```
node_modules
```

`.dockerignore` files are an easy way to selectively copy only image relevant files. You can read
more about this [here](https://docs.docker.com/engine/reference/builder/#dockerignore-file). In this
case, the `node_modules` folder should be omitted in the second `COPY` step because otherwise, it
would possibly overwrite files which were created by the command in the `RUN`
step. For further details on why this is recommended for Node.js applications and other best
practices, have a look at their guide
on [Dockerizing a Node.js web app](https://nodejs.org/en/docs/guides/nodejs-docker-webapp).

> `.dockerignore` 파일은 이미지 관련 파일만 선택적으로 복사할 수 있는 쉬운 방법입니다.
> 여기서 더 많은 것을 읽을 수 있습니다.
> 이번 케이스의 경우 `node_modules` 디렉토리는 `COPY` 단계에서 생략됩니다.
> `RUN` 단계에서 명령어에 의해서 생성된 파일을 덮어 쓸수 있습니다.
> Node.js 애플리케이션 및 기타 모범 사례에 권장되는 이유에 대한 자세한 내용은 Node.js 웹 애플리케이션 도커화에 대한 가이드를 참조해주세요

3. Build a new image using `docker build`.

> `docker build` 명령어를 사용하여 새로운 이미지를 빌드합니다.

```shell
$ docker build -t getting-started .
```

You should see output like this...

> 다음과 같은 결과를 볼 것입니다.

```shell
[+] Building 16.1s (10/10) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 175B
 => [internal] load .dockerignore
 => => transferring context: 2B
 => [internal] load metadata for docker.io/library/node:18-alpine
 => [internal] load build context
 => => transferring context: 53.37MB
 => [1/5] FROM docker.io/library/node:18-alpine
 => CACHED [2/5] WORKDIR /app
 => [3/5] COPY package.json yarn.lock ./
 => [4/5] RUN yarn install --production
 => [5/5] COPY . .
 => exporting to image
 => => exporting layers
 => => writing image     sha256:d6f819013566c54c50124ed94d5e66c452325327217f4f04399b45f94e37d25
 => => naming to docker.io/library/getting-started
```

You’ll see that all layers were rebuilt. Perfectly fine since we changed the Dockerfile quite a bit.

> 모든 레이어들이 재빌드된 것을 볼 것입니다. 우리가 Dockerfile을 아주 조금 변경했기 때문에 괜찮습니다.

4. Now, make a change to the `src/static/index.html` file (like change the `<title>` to say “The
   Awesome
   Todo App”).

> 지금 `src/static/index.html` 파일을 변경합니다. (<title> 태그에 "The Awesome Todo App"을 넣어봅니다)

5. Build the Docker image now using `docker build -t getting-started .` again. This time, your
   output
   should look a little different.

> `docker build -t getting-started .` 명령어를 사용하여 도커 이미지를 다시 빌드합니다.
> 여러분들의 출력은 결과가 약간 다를 것입니다.

```shell
[+] Building 1.2s (10/10) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 37B
 => [internal] load .dockerignore
 => => transferring context: 2B
 => [internal] load metadata for docker.io/library/node:18-alpine
 => [internal] load build context
 => => transferring context: 450.43kB
 => [1/5] FROM docker.io/library/node:18-alpine
 => CACHED [2/5] WORKDIR /app
 => CACHED [3/5] COPY package.json yarn.lock ./
 => CACHED [4/5] RUN yarn install --production
 => [5/5] COPY . .
 => exporting to image
 => => exporting layers
 => => writing image     sha256:91790c87bcb096a83c2bd4eb512bc8b134c757cda0bdee4038187f98148e2eda
 => => naming to docker.io/library/getting-started
```

First off, you should notice that the build was MUCH faster! And, you’ll see that several steps are
using previously cached layers. So, hooray! We’re using the build cache. Pushing and pulling this
image and updates to it will be much faster as well. Hooray!

> 일단은 여러분들은 빌드가 매우 빠른다는 것을 알것입니다. 그리고 이전에 여러 단계들이 이전에 캐싱된 레이어를 사용하는 것을 볼 수 있습니다.
> 우리는 빌드 캐시를 사용한 것입니다. 이 이미지를 Pushing과 Pulling하는 속도도 더 빨라집니다.

### 멀티 스테이지 빌드(Multi-stage builds)

While we’re not going to dive into it too much in this tutorial, multi-stage builds are an
incredibly powerful tool to help use multiple stages to create an image. There are several
advantages for them:

> 이 튜토리얼에서 너무 깊이 파고들지는 않겠지만, 멀티 스테이지 빌드는 여러 단계를 사용하여 이미지를 생성하는데 도움이 되는 매우 강력한 도구입니다.
> 멀티 스테이지 빌드에는 여러 장점이 있습니다.

- Separate build-time dependencies from runtime dependencies
- Reduce overall image size by shipping only what your app needs to run

> - 빌드 시간 종속성과 런타임 종속성을 분리합니다.
> - 앱에서 실행해야 하는 항목만 배송하여 전체 이미지 크기를 줄입니다.

### 메이븐/톰캣 예제(Maven/Tomcat example)

When building Java-based applications, a JDK is needed to compile the source code to Java bytecode.
However, that JDK isn’t needed in production. Also, you might be using tools like Maven or Gradle to
help build the app. Those also aren’t needed in our final image. Multi-stage builds help.

> 자바 기반 애플리케이션을 빌드할때 JDK는 소스 코드를 자바 바이트코드로 컴파일이 필요합니다.
> 그러나,JDK는 프로덕션에서는 필요하지 않습니다. 또한 앱 빌드를 도와주기 위해서 메이븐 또는 그레이들과 같은 도구를 사용할 수 있습니다.
> 메이븐이나 그레이들 또한 최종 이미지에서는 필요하지 않습니다.
> 멀티-스테이지가 빌드를 도와줍니다.

```dockerfile
# syntax=docker/dockerfile:1
FROM maven AS build
WORKDIR /app
COPY . .
RUN mvn package

FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/webapps
```

In this example, we use one stage (called `build`) to perform the actual Java build using Maven. In
the second stage (starting at FROM tomcat), we copy in files from the build stage. The final image
is only the last stage being created (which can be overridden using the `--target` flag).

> 이 예제에서는 메이븐을 사용하여 실제 자바 빌드를 수행하기 위해서 빌드라고 불리는 하나의 스테이지를 사용합니다.
> 두번째 스테이지(`FROM tomcat`에서 시작하는)에서는 우리들은 빌드 스테이지로부터 파일들을 복사합니다.
> 최종 이미지는 생성되는 마지막 단계(--target 옵션을 사용하여 재정의 할 수 있음)입니다.

### 리액트 예제(React example)

When building React applications, we need a Node environment to compile the JS code (typically JSX),
SASS stylesheets, and more into static HTML, JS, and CSS. If we aren’t doing server-side rendering,
we don’t even need a Node environment for our production build. Why not ship the static resources in
a static nginx container?

> 리액트 애플리케이션을 빌드할때 JS 코드(실제로는 JSX), SASS 스타일시트 등을 정적 HTML, JS, CSS로 컴파일 하기 위한 Node 환경이 필요합니다.
> 만약 우리가 서버-사이드 렌더링을 할 수 없다면 프로덕션 빌드를 위한 Node 환경이 필요치 않다.
> 정적 nginx 컨테이너에 정적 리소스를 전달할 수 없을까요?

```dockerfile
# syntax=docker/dockerfile:1
FROM node:18 AS build
WORKDIR /app
COPY package* yarn.lock ./
RUN yarn install
COPY public ./public
COPY src ./src
RUN yarn run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```

Here, we are using a node:18 image to perform the build (maximizing layer caching) and then copying
the output into an nginx container. Cool, huh?

> 여기서는 node:18 이미지를 사용하여 빌드(레이어 캐싱 최대화)를 수행한 다음에 nginx 컨테이너에 출력을 복사합니다.

### 다음 단계(Next steps)

By understanding a little bit about the structure of images, you can build images faster and ship
fewer changes. Scanning images gives you confidence that the containers you are running and
distributing are secure. Multi-stage builds also help you reduce overall image size and increase
final container security by separating build-time dependencies from runtime dependencies.

> 이미지의 구조에 대하여 약간 이해함으로써 빠른 이미지 빌드를 할수 있고 변경 사항을 더 적게 전송할 수 있습니다.
> 이미지를 스캔하면 실행 및 배포 중인 컨테이너가 안전하다는 확신을 얻을 수 있습니다.
> 멀티-스테이지 빌드를 사용하면 빌드 시간 종속성과 런타임 종속성을 구분하여 전체 이미지 크기를 줄이고 최종 컨테이너 보안을 강화할 수 있습니다.

In the next section, you’ll learn about additional resources you can use to continue learning about
containers.

> 다음장에서는 컨테이너에 대해서 배우는 것을 계속하는데 사용할 수 있는 추가 리소스에 대해서 배우게 됩니다.

