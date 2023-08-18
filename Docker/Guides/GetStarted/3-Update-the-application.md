## Update the applicatoin

In [part 2](2-Containerize-an-application.md), you containerized a todo application. In this part, you will update the
application and container image. You will also learn how to stop and remove a container.

> Part 2에서 여러분들은 투두 애플리케이션을 컨테이너화하였습니다. 이번 주제에서는 여러분들은 애플리케이션과 컨테이너 이미지를 업데이트할 것입니다.
> 여러분들은 컨테이너를 정지시키고 삭제하는 방법에 대해서 배울 것입니다.

### 소스 코드 업데이트하기(Update the source code)

In the steps below, you will change the “empty text” when you don’t have any todo list items to “You have no todo items
yet! Add one above!”

> 아래 단계들에서 여러분들은 작업 관리 항목이 없을 때 "empty text"를 “You have no todo items yet! Add one above!”로 변경할 것입니다.

1. In the src/static/js/app.js file, update line 56 to use the new empty text.

> `src/static/js/app.js` 파일에서, 새로운 빈 텍스트를 사용하기 위해서 56번째 줄을 업데이트합니다.

```
 ...
 -                <p className="text-center">No items yet! Add one above!</p>
 +                <p className="text-center">You have no todo items yet! Add one above!</p>
 ...
```

2. Build your updated version of the image, using the same docker build command you used in part 2.

> 파트 2에서 여러분들이 사용한 같은 도커 빌드 명령어를 사용하여 이미지의 업데이트된 버전을 빌드하세요.

```
$ docker build -t getting-started .
```

3. Start a new container using the updated code.

> 업데이트된 코드를 사용하여 새로운 컨테이너를 시작하세요.

```
$ docker run -dp 3000:3000 getting-started
```

You probably saw an error like this (the IDs will be different):

> 여러분들은 이와 같은 에러를 아마도 볼 것입니다. (아이디(ID)가 달라질 것입니다.)

```
docker: Error response from daemon: driver failed programming external connectivity on endpoint laughing_burnell 
(bb242b2ca4d67eba76e79474fb36bb5125708ebdabd7f45c8eaf16caaabde9dd): Bind for 0.0.0.0:3000 failed: port is already allocated.
```

The error occurred because you aren’t able to start the new container while your old container is still running. The
reason is that the old container is already using the host’s port 3000 and only one process on the machine (containers
included) can listen to a specific port. To fix this, you need to remove the old container.

> 여러분들의 예전 컨테이너가 여전히 동작하는 동안 새로운 컨테이너를 시작할 수 없기 때문에 에러가 발생한 것입니다.
> 에러 이유는 이전 컨테이너가 이미 호스트 머신의 3000포트를 사용중이기 때문입니다.
> 이 문제를 고치기 위해서는 여러분들은 이전 컨테이너를 제거해야 합니다.

### 이전 컨테이너 삭제하기(Remove the old container)

To remove a container, you first need to stop it. Once it has stopped, you can remove it. You can remove the old
container using the CLI or Docker Desktop’s graphical interface. Choose the option that you’re most comfortable with.

> 컨테이너를 삭제하기 위해서 여러분들은 우선 컨테이너를 멈추는 것이 필요합니다. 일단 컨테이너가 멈추면 여러분들은 컨테이너를 제거할 수 있습니다.
> 여러분들은 이전 컨테이너를 CLI 또는 도커 데스크톱의 GUI를 사용하여 제거할 수 있습니다.
> 여러분들이 가장 편한 방법을 선택해주세요

**CLI**

Remove a container using the CLI

> CLI를 사용하여 컨테이너를 제거하세요

1. Get the ID of the container by using the docker ps command.

> 도커 `ps` 명령어를 사용하여 컨테이너의 ID를 확인하세요.

```bash
$ docker ps
```

2. Use the docker stop command to stop the container. Replace <the-container-id> with the ID from docker ps.

> 컨테이너를 멈추기 위해서 도커 `stop` 명령어를 사용하세요.
> 도커 ps 명령어로부터 확인한 ID를 가지고 <the-container-id>를 대체하세요.

```bash
$ docker stop <the-container-id>
```

3. Once the container has stopped, you can remove it by using the docker rm command.

> 일단 컨테이너가 멈추면, 여러분들은 도커 `rm` 명령어를 사용하여 컨테이너를 제거할 수 있습니다.

```bash
$ docker rm <the-container-id>
```

Note

You can stop and remove a container in a single command by adding the force flag to the docker rm command. For example:
docker rm -f <the-container-id>

> 참고
> 도커 rm 명령어에서 강제 옵션(force flag)을 추가함으로써 단일 명령어로 컨테이너를 중지 및 삭제 할 수 있습니다.
> 예를 들어 `docker rm -f <the-container-id>`

### 업데이트된 앱 컨테이너를 시작하기(Start the updated app container)

1. Now, start your updated app using the docker run command.

> 지금, `docker run` 명령어를 사용하여 여러분들의 업데이트된 앱을 시작하세요.

2. Refresh your browser on http://localhost:3000 and you should see your updated help text.

> 여러분들의 브라우저(http://localhost:3000)를 새로고침하고 여러분들은 업데이트된 도움말을 확인하세요.

![](https://docs.docker.com/get-started/images/todo-list-updated-empty-text.png)

### Next steps

While you were able to build an update, there were two things you might have noticed:

- All of the existing items in your todo list are gone! That’s not a very good app! You’ll fix that shortly.
- There were a lot of steps involved for such a small change. In an upcoming section, you’ll learn how to see code
  updates without needing to rebuild and start a new container every time you make a change.

Before talking about persistence, you’ll see how to share these images with others.

> 여러분들은 업데이트를 빌드할 수 있는 동안, 다음과 같은 두가지 사항을 발견할 수 있습니다.
>
> - 여러분들의 작업 관리 리스트에서 존재하는 항목들 전체가 사라졌습니다. 이것은 좋은 앱은 아닙니다! 여러분들이 곧 고칠 것입니다.
> - 이와 같은 작은 변경을과 관련된 많은 단계들이 있습니다. 다가오는 섹션에서는 여러분들은 소스 코드를 변경 할때마다 새로운 컨테이너를 빌드하고 시작할 필요없이 코드 업데이트를 보는 방법을 배우게 될 것입니다.
>
> 지속성(persistence)에 대해서 배우기전에 여러분들은 이미지들은 다른 사람들과 공유하는 방법에 대해서 볼 것입니다.


