## Persist the DB

In case you didn’t notice, your todo list is empty every single time you launch the container. Why
is this? In this
part, you’ll dive into how the container is working.

> 컨테이너를 시작할 때마다 작업관리 목록이 비어있습니다.
> 왜 이런걸까요? 이 부분에서는 여러분들은 어떻게 컨테이너가 작동하는지에 대해서 알아볼 것입니다.

### 컨테이너의 파일 시스템(The container’s filesystem)

When a container runs, it uses the various layers from an image for its filesystem. Each container
also gets its own
“scratch space” to create/update/remove files. Any changes won’t be seen in another container, even
if they’re using the
same image.

> 컨테이너가 실행할 때, 컨테이너는 컨테이너의 파일 시스템에 대한 이미지로부터 다양한 레이어들을 사용합니다.
> 각 컨테이너에는 파일을 생성/수정/삭제할 수 있는 "스크래치 공간(scratch space)"이 있습니다.
> 컨테이너들이 같은 이미지를 사용해도 또 다른 컨테이너에는 어떠한 변화도 보이지 않습니다.

#### 실제로 보기(See this in practice)

To see this in action, you’re going to start two containers and create a file in each. What you’ll
see is that the files
created in one container aren’t available in another.

> 이것이 실제로 작동하는 것을 보기 위해서는 여러분들은 두개의 컨테이너를 시작할 것이고 각각의 컨테이너에 파일을 생성할 것입니다.
> 한 컨테이너에서 생성된 파일은 다른 컨테이너에서 사용할 수 없습니다.

1. Start an ubuntu container that will create a file named /data.txt with a random number between 1
   and 10000.

> 랜덤한 숫자 1~10000 사이의 숫자가 저장된 `/data.txt` 파일 이름을 생성하는 우분투 컨테이너를 시작하세요.

```shell
$ docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```

In case you’re curious about the command, you’re starting a bash shell and invoking two commands (
why you have the &&).
The first portion picks a single random number and writes it to /data.txt. The second command is
simply watching a file
to keep the container running.

> 여러분들이 명령어가 궁금하실 때를 대비해서, 여러분들은 bash 셸을 시작하고 2개의 명령어를 실행합니다.
> 첫번째 명령어 부분은 단일 랜덤 숫자를 선택하고 `/data.txt` 파일에 작성합니다.
> 두번째 명령어는 간단하게 컨테이너에서 실행하기 위해서 파일을 보는 것입니다.

2. Validate that you can see the output by accessing the terminal in the container. To do so, you
   can use the CLI or
   Docker Desktop’s graphical interface.

> 컨테이너의 터미널에 접근하여 출력을 볼수 있는지 확인합니다.
> 그러기 위해서 여러분들은 CLI 또는 도커 데스크톱의 GUI를 사용할 수 있습니다.

**CLI**

On the command line, use the docker exec command to access the container. You need to get the
container’s ID (use docker
ps to get it). In your Mac or Linux terminal, or in Windows Command Prompt or PowerShell, get the
content with the
following command.

> 명령어 라인에서, 컨테이너에 접근하기 위해서 `docker exec` 명령어를 사용하세요.
> 여러분들은 컨테이너의 ID(컨테이너 ID를 확인하기 위해서 `docker ps` 명령어를 사용하세요)가 필요합니다.
> Mac 또는 Linux 터미널에서 또는 윈도우 명령어 프롬프트 또는 PowerShell에서 다음 명령어를 사용하여 내용을 확인하세요.

```shell
$ docker exec <container-id> cat /data.txt
```

You should see a random number.

> 여러분들은 랜덤한 숫자를 확인해 볼 수 있습니다.

3. Now, start another ubuntu container (the same image) and you’ll see you don’t have the same file.
   In your Mac or
   Linux terminal, or in Windows Command Prompt or PowerShell, get the content with the following
   command.

> 같은 이미지에서 또다른 우분투 컨테이너를 시작하세요. 그리고 여러분들은 같은 파일이 없다는 것을 볼 것입니다.
> 여러분들의 Mac 또는 Linux 터미널, 윈도우 프롬프트 또는 PowerShell에서 다음 명령어를 사용하여 내용을 확인합니다.

```shell
$ docker run -it ubuntu ls /
```

In this case the command lists the files in the root directory of the container. Look, there’s no
data.txt file there!
That’s because it was written to the scratch space for only the first container.

> 컨테이너의 루트 디렉토리에 파일 목록을 확인하는 명령어를 수행했을 때, 보세요. 루트 경로에는 `data.txt` 파일이 없습니다.
> 왜냐하면 `data.txt` 파일은 오직 첫번째 컨테이너에 스크래치 공간(scratch space)에 저장되었기 때문입니다.

4. Go ahead and remove the first container using the `docker rm -f <container-id>` command.

> `docker rm -f <container-id>` 명령어를 사용해서 첫번째 컨테이너를 제거하세요.

### 컨테이너 볼륨(Container volumes)

With the previous experiment, you saw that each container starts from the image definition each time
it starts. While
containers can create, update, and delete files, those changes are lost when you remove the
container and Docker
isolates all changes to that container. With volumes, you can change all of this.

> 이전 실습에서 여러분들은 각각의 컨테이너가 같은 이미지 정의에서 시작되는 것을 보았습니다.
> 컨테이너들이 파일을 생성되고 수정되고 삭제하는 동안 이 변화들은 여러분들이 컨테이너를 삭제할 때 잃어버립니다. 그리고 도커는 컨테이너에서
> 발생하는 모든 변화들(파일의 생성 등)을 고립시킵니다.
> 볼륨(volumes)을 사용하면 여러분들은 이 모든 것을 변경할 수 있습니다.

[Volumes](https://docs.docker.com/storage/volumes/) provide the ability to connect specific
filesystem paths of the
container back to the host machine. If you mount a directory in the container, changes in that
directory are also seen
on the host machine. If you mount that same directory across container restarts, you’d see the same
files.

> **볼륨(Volumes)은 컨테이너의 특정 파일시스템 경로를 호스트 시스템에 연결할 수 있는 기능입니다.**
> 만약 여러분들이 컨테이너의 디렉토리에 마운트한다면 디렉토리안에서 발생하는 변경들은 호스트 머신에서 볼 수 있습니다.
> 만약 여러분들이 컨테이너를 다시 시작할 때 동일한 디렉토리를 마운트한다면 동일한 파일이 표시됩니다.

There are two main types of volumes. You’ll eventually use both, but you’ll start with volume
mounts.

> 볼륨의 종류에는 두가지가 있습니다.
> 여러분들은 두가지 다 골고루 사용할 것입니다.
> 그러나 여러분들은 볼륨 마운트를 가지고 시작할 것입니다.

### 작업관리 데이터 지속시키기(Persist the todo data)

By default, the todo app stores its data in a SQLite database at /etc/todos/todo.db in the
container’s filesystem. If
you’re not familiar with SQLite, no worries! It’s simply a relational database that stores all the
data in a single
file. While this isn’t the best for large-scale applications, it works for small demos. You’ll learn
how to switch this
to a different database engine later.

> 기본적으로 작업관리 앱은 컨테이너의 파일 시스템에서 `/etc/todos/todo.db`에 있는 SQLite 데이터베이스에 데이터를 저장합니다.
> 만약 여러분들이 SQLite에 익숙하지 않는다면 걱정하지 마십시오. SQLite는 하나의 단일 파일에 모든 데이터를 저장하는 간단한 관계형 데이터베이스입니다.
> SQLite가 큰 용량의 애플리케이션에 대한 좋은 데이터베이스는 아닌 반면에 SQLite는 작은 데모를 대상으로 작동하는데 좋습니다.
> 여러분들은 나중에 다른 데이터베이스 엔진으로 변경하는 것에 대해서 배울 것입니다.

With the database being a single file, if you can persist that file on the host and make it
available to the next
container, it should be able to pick up where the last one left off. By creating a volume and
attaching (often called
“mounting”) it to the directory where you stored the data, you can persist the data. As your
container writes to the
todo.db file, it will persist the data to the host in the volume.

> 데이터베이스가 단일 파일인 경우, 호스트에서 해당 파일을 유지하고 다음 컨테이너에서 사용할 수 있도록 설정할 수 있으면, 마지막 컨테이너가 중단된 위치에서 해당 파일을 선택할
> 수 있습니다.
> 볼륨을 생성하고 연결(종종 마운트라고 불리는)함으로써 데이터를 저장한 디렉토리로 이동시키면 데이터를 유지시킬 수 있습니다.
> 여러분들의 컨테이너는 `todo.db` 파일에 저장하기 때문에 볼륨의 호스트에 데이터를 유지시킬 것입니다.

As mentioned, you’re going to use a volume mount. Think of a volume mount as an opaque bucket of
data. Docker fully
manages the volume, including the storage location on disk. You only need to remember the name of
the volume.

> 설명한대로 여러분들은 볼륨 연결을 사용할 것입니다.
> 불륨 연결을 불투명한 데이터 버킷으로 생성하세요.
> 도커는 디스크에 저장된 위치를 포함하여 볼륨을 완벽하게 관리할 것입니다.
> 여러분들은 오직 볼륨의 이름을 기억하는 것만 필요합니다.

#### 볼륨을 생성하고 컨테이너 시작하기(Create a volume and start the container)

You can create the volume and start the container using the CLI or Docker Desktop’s graphical
interface.

> 여러분들은 CLI 또는 도커 데스크톱의 GUI를 이용하여 보륨을 생성하고 컨테이너를 시작할 수 있습니다.

**CLI**

1. Create a volume by using the `docker volume create` command.

> `docker volume create` 명령어를 사용하여 볼륨을 생성합니다.

```shell
$ docker volume create todo-db
```

2. Stop and remove the todo app container once again with `docker rm -f <id>`, as it is still
   running without using the
   persistent volume.

> 지속적인 볼륨을 사용없이 동작하는 컨테이너인 투두 앱 컨테이너를 `docker rm -f <id>` 명령어를 사용하여 중지하고 삭제하세요.

3. Start the todo app container, but add the --mount option to specify a volume mount. Give the
   volume a name, and mount
   it to /etc/todos in the container, which captures all files created at the path. In your Mac or
   Linux terminal, or in
   Windows Command Prompt or PowerShell, run the following command:

> 투두 앱 컨테이너를 시작하세요. 그러나 볼륨 연결을 명세하기 위해서 `--mount` 옵션을 추가하세요.
> 볼륨에 이름을 부여하고 볼륨을 컨테이너에 있는 `/etc/todos`에 연결하면 경로에서 생성된 모든 파일이 캡처됩니다.
> 맥 또는 리눅스 터미널, 윈도우 프롬프트 또는 파워셸에서 다음 명령을 실행하세요.

```shell
$ docker run -dp 3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```

#### 데이터 지속을 확인하기(Verify that the data persists)

1. Once the container starts up, open the app and add a few items to your todo list.

> 일단 컨테이너가 시작되면 앱을 열고 여러분들의 작업관리 리스트에 항목들을 몇개 추가하세요.

![](https://docs.docker.com/get-started/images/items-added.png)

2. Stop and remove the container for the todo app. Use Docker Desktop or docker ps to get the ID and
   then docker rm
   -f <id> to remove it.

> 투두 앱에 대한 컨테이너를 중지하고 삭제하세요.
> 도커 데스크톱이나 `docker ps`를 사용하여 컨테이너 ID를 확인하세요. 그리고나서 `docker rm -f <id>` 명령어를 사용하여 컨테이너를 제거하세요.

3. Start a new container using the same steps from above.

> 위의 동일한 단계를 사용하여 새로운 컨테이너를 시작하세요.

4. Open the app. You should see your items still in your list.

> 앱을 열고 여러분들은 여러분들의 작업관리 리스트에 여전히 동일한 항목들을 확인할 수 있을 것입니다.

5. Go ahead and remove the container when you’re done checking out your list.

> 리스트를 확인하였다면 컨테이너를 제거해주세요.

You’ve now learned how to persist data

> 여러분들은 지금 데이터를 지속시키는 방법에 대해서 배웠습니다.

### 볼륨에 자세히 알아보기(Dive into the volume)

A lot of people frequently ask “Where is Docker storing my data when I use a volume?” If you want to
know, you can use
the `docker volume inspect` command.

> 많은 사람들이 빈번하게 "내가 볼륨을 사용할때 도커는 내 데이터를 어디에 저장하나요?"와 같은 질문을 합니다.
> 만약 여러분들도 알고 싶다면, 여러분들은 `docker volume inspect` 명령어를 사용하여 확인할 수 있습니다.

```shell
$ docker volume inspect todo-db
[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

The `Mountpoint` is the actual location of the data on the disk. Note that on most machines, you
will need to have root
access to access this directory from the host. But, that’s where it is.

> 마운트포인트(MountPoint)는 디스크 위에 데이터 실제 위치입니다.
> 대부분의 컴퓨터들에서 이 디렉토리에 접근하려면 루트 접근 권한이 있어야 합니다. 그러나 그곳이 바로 그곳(/var/lib/docker/volumes/todo-db/_data)
> 입니다.


Accessing volume data directly on Docker Desktop

While running in Docker Desktop, the Docker commands are actually running inside a small VM on your
machine.
If you wanted to look at the actual contents of the mount point directory, you would need to look
inside of that VM.

> 도커 데스크톱에서 볼륨 데이터를 직접 접근하기
>
> 도커 데스크톱에서 실행하는 동안 도커 명령어들은 여러분들의 컴퓨터 위에서 작은 VM 안에서 실행중입니다.
> 만약 여러분들이 마운드 포인트 디렉토리의 실제 내용물을 확인하고 싶다면 여러분들은 VM 내부를 살펴봐야 합니다.

### 다음 단계(Next steps)

At this point, you have a functioning application that can survive restarts.

However, you saw earlier that rebuilding images for every change takes quite a bit of time. There’s
got to be a better
way to make changes, right? With bind mounts, there is a better way.

> 이 시점에서 다시 시작해도 작동하는 응용 프로그램이 있습니다.
>
> 그러나 여러분들은 모든 변경 사항에 대해서 이미지를 다시 빌드하는데는 많은 시간이 걸린다는 것을 앞에서 살펴봤습니다.
> 거기에는 변경들을 만들기 위한 더 좋은 방법이 있을 겁니다. 그렇죠? 바인드 마운트를 사용하면 더 낳은 방법이 있습니다.




