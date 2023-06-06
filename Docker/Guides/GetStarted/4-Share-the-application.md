## Share the application

Now that you’ve built an image, you can share it. To share Docker images, you have to use a Docker registry. The default
registry is Docker Hub and is where all of the images you’ve used have come from.

> 지금 여러분들은 이미지를 빌드하였으므로 공유할 수 있습니다. 도커 이미지를 공유하기 이해서는 여러분들은 도커 레지스트리(registry)를 사용해야 합니다.
> 기본적인 레지스트리는 도커 허브(Docker Hub)이고 여러분들이 사용한 모든 이미지들의 원본입니다.

Docker ID

A Docker ID allows you to access Docker Hub which is the world’s largest library and community for container images.
Create a Docker ID for free if you don’t have one.

> 도커 아이디
>
> 도커 아이디는 여러분들에게 컨테이너 이미지를 위한 커뮤니티이자 세계의 가장 큰 라이브러리인 도커 허브에 접근하는 것을 허용합니다.
> 만약 여러분들이 도커 아이디를 가지고 있지 않다면 무료로 도커 아이디를 생성하세요.

## 앱 생성하기(Create a repo)

To push an image, you first need to create a repository on Docker Hub.

> 이미지를 푸시하기 위해서 여러분들은 도커 허브에 저장소를 생성하는 것이 우선 필요합니다.

1. [Sign up](https://hub.docker.com/signup?state=hKFo2SBWb0dubDRscXBVQ2VPbXJNa3ZwdXFOYWJVZndoOFplQ6Fur3VuaXZlcnNhbC1sb2dpbqN0aWTZIGEydUhpaTBEaTBHalJaTmFWbnVybENtZ1RPYmtVVXhUo2NpZNkgbHZlOUdHbDhKdFNVcm5lUTFFVnVDMGxiakhkaTluYjk)
   or Sign in to [Docker Hub](https://hub.docker.com/).

> 도커 허브에 로그인 또는 회원가입합니다.

2. Select the **Create Repository** button.

> 저장소 생성 버튼을 선택합니다.

3. For the repo name, use getting-started. Make sure the Visibility is Public.

> 저장소 이름에 `getting-started`을 넣으세요. 가시성(Visibility)을 public으로 선택합니다.

```
Private repositories

Did you know that Docker offers private repositories which allows you to restrict content to specific users or teams? 
Check out the details on the Docker pricing page.
```

> 개인 저장소
> 도커가 특정 사용자 또는 팀으로 콘텐츠를 제한할 수 있는 개인 저장소를 제공한다는 사실을 알고 계시나요?
> 도커 요금 페이지에서 자세한 내용을 확인해보세요.

4. Select the Create button.

> 생성 버튼을 클릭하세요.

If you look at the image below an example Docker command can be seen. This command will push to this repo.

> 아래 이미지를 보시면 도커 명령어의 예시를 볼 수 있습니다. 이 명령어는 이 저장소에 푸시할 것입니다.

![](https://docs.docker.com/get-started/images/push-command.png)

### 이미지 푸시하기(Push the image)

1. In the command line, try running the push command you see on Docker Hub. Note that your command will be using your
   namespace, not “docker”.

> 명령어 라인에서 도커 허브에 표시되는 `push` 명령을 실행해보세요.
> 여러분들의 명령어는 "docker"가 아닌 여러분들의 네임스페이스를 사용할 것입니다.

```shell
 $ docker push docker/getting-started
 The push refers to repository [docker.io/docker/getting-started]
 An image does not exist locally with the tag: docker/getting-started
```

Why did it fail? The push command was looking for an image named docker/getting-started, but didn’t find one. If you run
docker image ls, you won’t see one either.

> 왜 실패했을까요? 그 push 명령어는 docker/getting-started라는 이름의 이미지를 탐색하였습니다.
> 그러나, 찾지 못하였습니다. 만약 여러분이 `docker image ls` 명령어를 실행했다면, 여러분들은 하나도 볼수 없을 것입니다.

To fix this, you need to “tag” your existing image you’ve built to give it another name.

> 이 문제를 고치기 위해서, 여러분들이 빌드한 기존 이미지에 다른 이름을 붙이기 위해서 "태그"를 지정해야 합니다.

2. Login to the Docker Hub using the command docker login -u YOUR-USER-NAME.

> `docker login -u YOUR-USER-NAME` 명령어를 사용하여 도커 허브에 로그인하세요.
> 도커 18 버전 이후에는 아래와 같은 명령어를 통해서 도커 허브에 로그인하세요.

```
$ vim ~/my_password.txt
{도커 허브 비밀번호 작성}
$ cat ~/my_password.txt | docker login --username {도커허브 사용자이름} --password-stdin
```

3. Use the `docker tag` command to give the getting-started image a new name. Be sure to swap out `YOUR-USER-NAME` with
   your
   Docker ID.

> `getting-started` 이미지에 새로운 이름을 붙이기 위해서 `tag` 도커 명령어를 사용하세요.
> YOUR-USER-NAME은 여러분들의 도커 아이디로 대체하세요.

```shell
$ docker tag getting-started YOUR-USER-NAME/getting-started
```

To learn more about the `docker tag` command,
see [docker tag](https://docs.docker.com/engine/reference/commandline/tag/).

> `docker tag` 명령어에 대해서 더 알고싶다면, [docker tag](https://docs.docker.com/engine/reference/commandline/tag/)를 보세요.

4. Now try your push command again. If you’re copying the value from Docker Hub, you can drop the tagname portion, as
   you didn’t add a tag to the image name. If you don’t specify a tag, Docker will use a tag called latest.

> 여러분들의 `push` 명령어를 다시 시도해보세요.
> 여러분들은 이미지 이름에 태그를 추가하지 않았기 때문에 만약 여러분둘의 도커 허브로부터 값을 복사하는 중이라면 여러분들은 `tagname` 부분을 제거할 수 있습니다.  
> 만약 여러분들이 태그를 명세하지 않았다면, 도커는 `latest`라고 불리는 태그를 사용할 것입니다.

```shell
$ docker push YOUR-USER-NAME/getting-started
```

### 새로운 인스턴스에서 이미지를 실행시기키(Run the image on a new instance)

Now that your image has been built and pushed into a registry, try running your app on a brand new instance that has
never seen this container image. To do this, you will use Play with Docker.

> 지금 여러분들의 이미지는 빌드되었고 레지스트리로 푸시되었습니다. 이 컨테이너 이미지를 본적이 없는 새로운 인스턴스에서 앱을 실행해보세요.
> 이것을 하기 위해서 여러분들은 `Play with Dodcker`를 사용할 것입니다.

Note

Play with Docker uses the amd64 platform. If you are using an ARM based Mac with Apple Silicon, you will need to rebuild
the image to be compatible with Play with Docker and push the new image to your repository.

To build an image for the amd64 platform, use the --platform flag.

```shell
$ docker build --platform linux/amd64 -t YOUR-USER-NAME/getting-started .
```

Docker buildx also supports building multi-platform images. To learn more,
see [Mult-platform images](https://docs.docker.com/build/building/multi-platform/).

> 참고
> 
> `Play with Docker`는 amd64 플랫폼을 사용합니다. 만약 여러분들이 Apple Silicon이 있는 Mac 기반 ARM을 사용 중이라면,
> 여러분들은 여러분들의 저장소에 새로운 이미지를 푸시하고 `Play with Docker`와 호환되기 위해서 이미지를 재빌드해야 합니다.
> amd64 플랫폼을 대상으로 이미지를 빌드하기 위해서 `--platform` 플래그를 사용하세요.

```shell
$ docker build --platform linux/amd64 -t YOUR-USER-NAME/getting-started .
```

> Docker buildx는 다중 플랫폼 이미지 빌드도 지원합니다. 더 보기 위해서는 Mult-platform images을 봐주세요.

1. Open your browser to
   [Play with Docker](https://labs.play-with-docker.com/?_gl=1*i7shvq*_ga*MTY2MzgyMjEzLjE2ODU2OTQyNTM.*_ga_XJWPQMJYHQ*MTY4NTY5NDI1My4xLjEuMTY4NTY5NDMxNC42MC4wLjA.)

> Play with Docker를 여러분들의 브라우저로 여세요.

2. Select **Login** and then select **docker** from the drop-down list.

> 로그인 한후에 드롭다운 리스트로부터 도커를 선택합니다.

3. Connect with your Docker Hub account.

> 여러분들의 도커 허브 계정으로 연결하세요.

4. Once you’re logged in, select the **ADD NEW INSTANCE** option on the left side bar. If you don’t see it, make your
   browser a little wider. After a few seconds, a terminal window opens in your browser.

> 일단 로그인하면 왼쪽 사이드 바에서 ADD NEW INSTANCE를 선택하세요.
> 만약 보이지 않는다면 브라우저를 넓게 늘려보세요. 몇 초후에 여러분들의 브라우저에 터미널 창이 열릴 것입니다.

![](https://docs.docker.com/get-started/images/pwd-add-new-instance.png)

5. In the terminal, start your freshly pushed app.

> 터미널에서 여러분들의 방금 푸시한 애플리케이션을 실행해보세요.

```shell
$ docker run -dp 3000:3000 YOUR-USER-NAME/getting-started
```

You should see the image get pulled down and eventually start up.

> 이미지를 다운받아 시작되는 것을 볼 수 있습니다.

6. Select on the 3000 badge when it comes up and you should see the app with your modifications. If the 3000 badge
   doesn’t show up, you can select on the Open Port button and type in 3000.

> 컨테이너가 시작되면 3000번 배지를 선택하세요. 여러분들은 여러분의 수정들을 볼 수 있습니다.
> 만약 3000 배지가 보이지 않는다면 여러분들은 Open Port 버튼에 3000번을 선택할 수 있습니다.

### 다음 단계(Next steps)

In this section, you learned how to share your images by pushing them to a registry. You then went to a brand new
instance and were able to run the freshly pushed image. This is quite common in CI pipelines, where the pipeline will
create the image and push it to a registry and then the production environment can use the latest version of the image.

> 이번 주제에서는, 여러분들은 레지스트리에 푸사된 이미지를 공유하는 방법에 대해서 배웠습니다.
> 그런다음 여러분들은 완전히 새로운 인스턴스로 이동하여 새로 푸시된 이미지를 실행할 수 있었습니다.
> 이것은 CI(Continuous Integration) 파이프라인에서 매우 일반적이며, 파이프라인은 이미지를 생성하고
> 레지스트리에 푸시하면 제품(production) 환경에서 이미지의 최신 버전을 사용할 수 있습니다.

Now you can circle back around to what you noticed at the end of the last section. As a reminder, you noticed that when
you restarted the app, you lost all of your todo list items. That’s obviously not a great user experience, so next
you’ll learn how you can persist the data across restarts.

> 지금 여러분드은 마지막 세션의 끝에 표시된 내용으로 돌아가 볼 수 있습니다. 기억한대로, 여러분들은 여러분이 애플리케이션을 재시작할때 알림받은 것처럼
> 여러분들은 여러분들의 작업 리스트 항목 전체를 잃어버렸습니다. 그것은 매우 좋은 사용자 경험은 아닙니다. 그래서 다음 시간에는 여러분들은
> 어떻게 하면 여러분들이 재시작 후에도 데이터를 유지시킬 수 있을지 배울 것입니다.



