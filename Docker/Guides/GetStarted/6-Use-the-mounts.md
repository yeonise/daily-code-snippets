## Use the mounts

In [part 5](https://github.com/yeonise/daily-code-snippets/blob/main/Docker/Guides/GetStarted/5-Persist-the-DB.md)
, you
used a volume mount to persist the data in your database. A volume mount is a great choice when you
need
somewhere persistent to store your application data.

> Part5에서는 여러분들은 데이터베이스에 데이터를 지속시키기 위한 볼륨 연결을 사용하였습니다.
> 볼륨 연결은 여러분들이 애플리케이션 데이터를 어딘가 저장이 필요할때 좋은 선택입니다.

A bind mount is another type of mount, which lets you share a directory from the host’s filesystem
into the container.
When working on an application, you can use a bind mount to mount source code into the container.
The container sees the
changes you make to the code immediately, as soon as you save a file. This means that you can run
processes in the
container that watch for filesystem changes and respond to them.

> **바인드 마운트는 호스트의 파일 시스템에서 컨테이너로 디렉토리를 공유할 수 있는 마운트의 다른 유형입니다.**
> 애플리케이션이 동작할 때 여러분들은 소스 코드를 컨테이너로 바인드 마운트할 수 있습니다.
> 컨테이너는 파일이 저장되자마자 즉시 코드 변경에 대한 내용을 확인합니다.
> 즉, 컨테이너에서 파일 시스템의 변경사항을 감시하고 이에 응답하는 프로세스를 실행할 수 있다는 것을 의미합니다.

In this chapter, you’ll see how you can use bind mounts and a tool called nodemon to watch for file
changes, and then
restart the application automatically. There are equivalent tools in most other languages and
frameworks.

> 이번 장에서는 여러분들은 어떻게 바인드 마운트를 사용하고 `nodemon`이라고 불리는 도구를 사용하여 파일의 변경 사항을 모니터링한다음에 자동으로 애플리케이션을 다시 시작하는
> 방법에 대해 설명합니다.
> 그 도구들은 다른 대부분의 언어와 프레임워크에서 사용하는 동일한 도구들입니다.

### 빠른 볼륨 종류 비교(Quick volume type comparisons)

The following table outlines the main differences between volume mounts and bind mounts.

> 다음 테이블은 볼륨 마운트와 바인드 마운트간에 차이점입니다.

|                                              | Named volumes                                    | Bind mounts                                        |
|----------------------------------------------|--------------------------------------------------|----------------------------------------------------|
| Host location                                | Docker chooses                                   | You decide                                         |
| Mount Example(using --mount)                 | type=volume,src=my-volume,target=/usr/local/data | type=bind,src=/path/to/data,target=/usr/local/data |
| Populates new volume with container contents | Yes                                              | No                                                 |
| Supports Volume Drivers                      | Yes                                              | No                                                 |

|                          | 볼륨                                               | 바인드 마운트                                            |
|--------------------------|--------------------------------------------------|----------------------------------------------------|
| 호스트 위치                   | 도커 선택                                            | 사용자 선택                                             |
| 마운트 예시(--mount 옵션을 사용한)  | type=volume,src=my-volume,target=/usr/local/data | type=bind,src=/path/to/data,target=/usr/local/data |
| 컨테이너 내용들은 가진 새로운 볼륨을 꺼내기 | Yes                                              | No                                                 |
| 볼륨 드라이버 지원               | Yes                                              | No                                                 |

### 바인드 마운트 시도하기(Trying out bind mounts)

Before looking at how you can use bind mounts for developing your application, you can run a quick
experiment to get a
practical understanding of how bind mounts work.

> 애플리케이션 개발을 위해 바인드 바운트를 어떻게 사용하는지에 대해서 알아보기 전에 어떻게 바인드 마운트가 작동하는지에 대한 예제를 보겠습니다.

1. Open a terminal and change directory to the `app` directory of the getting started repository.

> 터미널을 열고 getting started 저장소의 `app` 디렉토리로 현재 디렉토리를 이동합니다.

2. Run the following command to start `bash` in an `ubuntu` container with a bind mount.

> 바인드 마운트를 가진 우분투 컨테이너에서 bash 셸을 시작하세요.

```shell
$ docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash
```

The `--mount` option tells Docker to create a bind mount, where `src` is the current working
directory on your host
machine (`getting-started/app`), and `target` is where that directory should appear inside the
container (`/src`).

> 바인드 마운트를 생성하기 위해서 Docker 명령어로 `--mount` 옵션을 설정합니다. src 디렉토리는 여러분의 호스트 컴퓨터(`getting-started/app`)의
> 현재 작업중인 디렉토리입니다.
> 그리고 `target`은 컨테이너(`/src`) 안에 있는 디렉토리입니다.

3. After running the command, Docker starts an interactive bash session in the root directory of the
   container’s
   filesystem.

> 명령어 실행 후에 도커는 도커는 컨테이너의 파일시스템의 루트 디렉토리에서 bash 셸을 시작합니다.

```shell
root@ac1237fad8db:/# pwd
/
root@ac1237fad8db:/# ls
bin   dev  home  media  opt   root  sbin  srv  tmp  var
boot  etc  lib   mnt    proc  run   src   sys  usr****
```

4. Change directory to the src directory.

> src 디렉토리로 이동합니다.

This is the directory that you mounted when starting the container. Listing the contents of this
directory displays the
same files as in the getting-started/app directory on your host machine.

> src 디렉토리는 컨테이너를 시작할때 여러분이 마운트한 디렉토리입니다.
> src 디렉토리의 내용들을 나열하면 호스스 컴퓨터의 `getting-started/app`과 동일한 파일이 표시됩니다.

```shell
root@ac1237fad8db:/# cd src
root@ac1237fad8db:/src# ls
Dockerfile  node_modules  package.json  spec  src  yarn.lock
```

5. Create a new file named `myfile.txt`.

> `myfile.txt` 파일을 생성합니다.

```
root@ac1237fad8db:/src# touch myfile.txt
root@ac1237fad8db:/src# ls
Dockerfile  myfile.txt  node_modules  package.json  spec  src  yarn.lock
```

6. Open the `app` directory on the host and observe that the myfile.txt file is in the directory.

> 호스트 머신에 있는 `app` 디렉토리를 열고 `myfile.txt`을 확인합니다.

```
├── app/
│ ├── Dockerfile
│ ├── myfile.txt
│ ├── node_modules/
│ ├── pacakge.json
│ ├── spec/
│ ├── src/
│ └── yarn.lock
```

7. From the host, delete the myfile.txt file.

> 호스트 머신에 있는 `myfile.txt` 파일을 제거합니다.

8. In the container, list the contents of the app directory once more. Observe that the file is now
   gone.

> 컨테이너에서 `app` 디렉토리의 내용들을 리스트화합니다. myfile.txt 파일이 없는지 확인합니다.

```shell
root@ac1237fad8db:/src# ls
Dockerfile  node_modules  package.json  spec  src  yarn.lock
```

9. Stop the interactive container session with Ctrl + D.

> `Ctrl` + `D`를 눌러 컨테이너의 셸을 중지합니다.

That’s all for a brief introduction to bind mounts. This procedure demonstrated how files are shared
between the host and the container, and how changes are immediately reflected on both sides. Now you
can use bind mounts to develop software.

> 이상으로 바인드 마운트에 대한 간단한 소개를 마칩니다.
> 이 단계에서는 호스트와 컨테이너간 파일 공유가 어떻게 공유되는지 시연하였습니다. 그리고 어떻게 양쪽에 즉시 반영되는지도 알아봤습니다.
> 지금 여러분들은 소프트웨어 개발에 바인드 마운트를 사용할 수 있습니다.

### 컨테이너 개발(Development containers)

Using bind mounts is common for local development setups. The advantage is that the development
machine doesn’t need to have all of the build tools and environments installed. With a single docker
run command, Docker pulls dependencies and tools.

> 바인드 마운트를 사용하는 것은 로컬한 개발 단계에서 일반적입니다.
> 개발하는 컴퓨터의 장점은 빌드 도구나 환경들을 전부 설치할 필요가 없다는 장점입니다.
> 한번의 도커 실행 명령으로 도커는 종속성과 도구들을 가져옵니다.

#### 개발 컨테이너에서 여러분들의 앱을 실행시키기(Run your app in a development container)

The following steps describe how to run a development container with a bind mount that does the
following:

- Mount your source code into the container
- Install all dependencies
- Start `nodemon` to watch for filesystem changes

> 다음 단계들은 다음과 같이 바인드 마운트를 가진 개발 컨테이너를 실행시키는 방법에 대해서 설명합니다.
> - 여러분들의 소스 코드를 컨테이너로 마운트하기
> - 모든 의존성 설치하기
> - 파일 시스템의 변경들을 감지하는 `nodemon` 시작하기

You can use the CLI or Docker Desktop to run your container with a bind mount.

> CLI 또는 도커 데스크톱을 사용하여 바인드 마운트를 가진 컨테이너를 실행시킬수 있습니다.

**CLI**

1. Make sure you don’t have any `getting-started` containers currently running.

> 현재 실행중인 getting-started 컨테이너들이 없는지 확인합니다.

2. Run the following command from the` getting-started/app` directory.

> `getting-started/app` 디렉토리에서 다음 명령어를 실행합니다.

Mac/Linux

```shell
$ docker run -dp 3000:3000 -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```

Windows

```shell
$ docker run -dp 3000:3000 `
    -w /app --mount "type=bind,src=$pwd,target=/app" `
    node:18-alpine `
    sh -c "yarn install && yarn run dev"
```

The following is a breakdown of the command:

- -dp 3000:3000 - same as before. Run in detached (background) mode and create a port mapping
- -w /app - sets the “working directory” or the current directory that the command will run from
- --mount type=bind,src="$(pwd)",target=/app - bind mount the current directory from the host into
  the /app directory in the container
- node:18-alpine - the image to use. Note that this is the base image for your app from the
  Dockerfile
- sh -c "yarn install && yarn run dev" - the command. You’re starting a shell using sh (alpine
  doesn’t have bash) and running yarn install to install packages and then running yarn run dev to
  start the development server. If you look in the package.json, you’ll see that the dev script
  starts nodemon.

> 다음은 명령어의 세부사항입니다.
> - `-dp 3000:3000` : 이전과 동일합니다. 백그라운드 모드로 실행 및 포트 매핑을 생성합니다.
> - `-w /app` : 명령이 실행될 "작업 디렉토리" 또는 현재 디렉토리를 설정합니다.
> - `--mount type=bind,src="$(pwd)",target=/app` : 호스트의 현재 디렉토리를 컨테이너의 `/app` 디렉토리로 바인드 마운트합니다.
> - `node:18-alpine` : 사용할 이미지입니다. 이것은 Dockerfile로부터 만든 여러분의 앱에 대한 기반 이미지입니다.
> - `sh -c "yarn install && yarn run dev"` : 명령어입니다. 여러분들은 `sh`(alpine은 `bash` 셸을 사용하지 않습니다.)을 사용하여
    셸을 시작하고 `yarn install`을 실행하여 패키지를 설치하고 `yarn run dev`를 실행하여 개발 서버를 시작합니다. 만약 여러분이 `package.json`
    을
    본다면 여러분들은 `nodemon`을 시작하는 `dev` 스크립트를 볼수 있습니다.

3. You can watch the logs using `docker logs <container-id>`. You’ll know you’re ready to go when
   you
   see this:

> `docker logs <container-id>`를 사용하여 로그를 볼 수 있습니다.
> 여러분들은 다음과 같은 정보를 확인하면 준비가 완료되었음을 알 수 있습니다.

```shell
$ docker logs -f <container-id>
nodemon src/index.js
[nodemon] 2.0.20
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] starting `node src/index.js`
Using sqlite database at /etc/todos/todo.db
Listening on port 3000
```

When you’re done watching the logs, exit out by hitting Ctrl+C.

> 여러분들이 로그를 보고 싶지 않다면 `Crtl + C`를 누르고 나가세요.

### 개발 컨테이너로 앱 개발하기(Develop your app with the development container)

Update your app on your host machine and see the changes reflected in the container.

> 호스트 머신에서 여러분들의 앱을 수정하고 컨테이너에 변경이 반영되었는지 확인하세요.

1. In the src/static/js/app.js file, on line 109, change the “Add Item” button to simply say “Add”:

> `src/static/js/app.js` 파일에서 109 라인에서 "Add Item"을 "Add"로 변경하세요.

```shell
- {submitting ? 'Adding...' : 'Add Item'}
+ {submitting ? 'Adding...' : 'Add'}
```

2. Refresh the page in your web browser, and you should see the change reflected almost immediately.
   It might take a few seconds for the Node server to restart. If you get an error, try refreshing
   after a few seconds.

> 웹 브라우저에서 페이지를 새로고침하세요. 그리고 즉시 변경이 반영된 것을 볼수 있습니다.
> 재시작하는데 Node server는 몇초 걸립니다. 만약 에러가 나면, 몇초후에 다시 새로고침해보세요.

![](https://docs.docker.com/get-started/images/updated-add-button.png)

3. Feel free to make any other changes you’d like to make. Each time you make a change and save a
   file, the `nodemon` process restarts the app inside the container automatically. When you’re
   done,
   stop the container and build your new image using:

> 다른 변경 사항이 있다면 언제든지 변경할 수 있습니다.
> 파일을 변경하고 저장할때마다 `nodemon` 프로세스가 자동으로 컨테이너 내부의 앱을 다시 시작합니다.
> 작업을 완료하면 다음을 사용하여 컨테이너를 중지하고 새 이미지를 작성합니다.

```shell
$ docker build -t getting-started .
```

### 다음 단계(Next steps)

At this point, you can persist your database and see changes in your app as you develop without
rebuilding the image.

> 이번장에서는 여러분들은 데이터베이스를 지속시키고 이미지의 재빌드없이 개발중인 앱의 변경사항을 확인할 수 있었습니다.

In addition to volume mounts and bind mounts, Docker also supports other mount types and storage
drivers for handling more complex and specialized use cases. To learn more about the advanced
storage concepts, see [Manage data in Docker](https://docs.docker.com/storage/).

> 볼륨 마운트와 바인드 마운트 외에도 도커는 또한 다른 마운트 타입들과 더 복잡하고 전문화된 유즈 케이스를 다루는 스토리지 드라이버를 지원합니다.
> 자세한 스토리지 개념을 더 배우고 싶다면 [도커에서 데이터 관리하기](https://docs.docker.com/storage/)를 봐주세요.

In order to prepare your app for production, you need to migrate your database from working in
SQLite to something that can scale a little better. For simplicity, you’ll keep using a relational
database and switch your application to use MySQL. But, how should you run MySQL? How do you allow
the containers to talk to each other? You’ll learn about that in the next section.

> 프로덕션을 위한 앱을 준비하기 위해서 SQLite에서 작업하던 데이터베이스를 확장성이 조금 더 뛰어난 데이터베이스로 마이그레이션 해야 합니다.
> 단순화를 위해 관계형 데이터베이스를 계속 사용하고 MySQL을 사용하도록 애플리케이션을 변경해야 합니다.
> 그러나 MySQL을 어떻게 실행해야 합니까? 어떻게 여러분들은 컨테이너들이 서로 통신할 수 있도록 하는 방법은 무엇입니까?
> 여러분들은 다음 장에서 배울것입니다.







