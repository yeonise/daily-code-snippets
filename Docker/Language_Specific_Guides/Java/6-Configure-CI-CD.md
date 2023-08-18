## Configure CI/CD for your application

### GitHub Actions을 가지고 시작하기(Get started with GitHub Actions)

This tutorial walks you through the process of setting up and using Docker GitHub Actions for
building Docker images, and pushing images to Docker Hub. You will complete the following steps:

> 이 튜토리얼에서는 도커 이미지를 빌드하고 이미지를 도커 허브로 푸시하기 위해서 도커 Github Actions을 설정 및 사용하는 과정을 안내합니다.

1. Create a new repository on GitHub.
2. Define the GitHub Actions workflow.
3. Run the workflow.

> 1. GitHub에 새로운 저장소를 생성합니다.
> 2. GitHub Actions 워크플로우를 정의합니다.
> 3. 워크플로우를 수행합니다.

To follow this tutorial, you need a Docker ID and a GitHub account.

> 이 튜토리얼을 따라가기 위해서는 여러분은 Docker ID와 GitHub 계정이 필요합니다.

### 첫번째 단계: 저장소 생성하기(Step one: Create the repository)

Create a GitHub repository and configure the Docker Hub secrets.

> GitHub 저장소를 생성하고 도커 허브 secrets을 설정합니다.

1. Create a new GitHub repository
   using [this template repository](https://github.com/dvdksn/clockbox/generate).

The repository contains a simple Dockerfile, and nothing else. Feel free to use another repository
containing a working Dockerfile if you prefer.

> 새로운 GitHub 저장소를 템플릿 저장소를 사용하여 생성합니다.
> 저장소는 간단한 Dockerfile만 포함하고 그외에는 없습니다.
> 만약 여러분이 원하면 Dockerfile을 폼하하는 또다른 저장소를 사용하는 것도 좋습니다.

2. Open the repository **Settings**, and go to **Secrets and variables** > **Actions**.

> 저장소 Settings를 열고, Secrets and variables > Actions로 이동하세요.

3. Create a new secret named `DOCKERHUB_USERNAME` and your Docker ID as value.

> DOCKERHUB_USERNAME이라는 새로운 secret을 생성하고 값에 여러분들의 도커 ID를 넣습니다.

4. Create a new Personal Access Token (PAT) for Docker Hub. You can name this token clockboxci.

> 도커 허브에 대한 새로운 Personal Access Token을 생성합니다.
> 여러분들은 이 토큰을 clockboxci라고 네이밍할 수 있습니다.

5. Add the PAT as a second secret in your GitHub repository, with the name DOCKERHUB_TOKEN.

> 여러분들의 GitHub 저장소에 두번째 secret으로서 PAT를 추가하세요. 이름은 DOCKERHUB_TOKEN입니다.

With your repository created, and secrets configured, you’re now ready for action!

> 저장소가 생성되고 secrets를 설정하고 여러분들은 액션을 할 준비가 되었습니다.

### 2단계: 워크플로우 준비하기(Step two: Set up the workflow)

Set up your GitHub Actions workflow for building and pushing the image to Docker Hub.

> 도커 허브에 이미지를 빌드하고 푸시하기 위해서 GitHub Actions 워크 플로우를 준비하세요.

1. Go to your repository on GitHub and then select the **Actions** tab.

> GitHub에 저장소로 이동하고나서 Actions 탭을 선택하세요.

2. Select set up a workflow yourself.

This takes you to a page for creating a new GitHub actions workflow file in your repository, under
.github/workflows/main.yml by default.

> 2. set up a workflow yourself 링크를 선택합니다.
     > 기본적으로 .github/workflows/main.yml 아래의 위치에 새로운 깃허브 액션 워크플로우를 생성하는 페이지가 나타납니다.

3. In the editor window, copy and paste the following YAML configuration.

> 편집창에서 YAML 설정에 다음과 같이 복사 붙여넣기 합니다.

```yaml
name: ci

on:
  push:
    branches:
      - "main"

jobs:
  build:
    runs-on: ubuntu-latest
```

- name: the name of this workflow.
- on.push.branches: specifies that this workflow should run on every push event for the branches in the list.
- jobs: creates a job ID (build) and declares the type of machine that the job should run on.

> - name : 워크플로우의 이름
> - on.push.branches : 브랜치에 푸시 이벤트 발생시 워크 플로우가 CI를 수행할 브랜치 목록
> - jobs : 작업 ID를 생성하고 작업을 실행해야 하는 시스템 타입을 선언합니다.

For more information about the YAML syntax used here,
see [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
.

> YAML 문법에 대한 더 많은 정보는 여기를 참고해주세요.

### 3단계: 워크플로우 단계들 선언하기(Step three: Define the workflow steps)

Now the essentials: what steps to run, and in what order to run them.

> 핵심은 실행 단게와 실행 순서입니다.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v3
      -
        name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      -
        name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/clockbox:latest
```

The previous YAML snippet contains a sequence of steps that:

> 이전 YAML snippet에는 다음과 같은 단계가 포함되어 있습니다.

1. Checks out the repository on the build machine.
2. Signs in to Docker Hub, using the [Docker Login](https://github.com/marketplace/actions/docker-login) action and your
   Docker Hub credentials.
3. Creates a BuildKit builder instance using
   the [Docker Setup Buildx](https://github.com/marketplace/actions/docker-setup-buildx) action.
4. Builds the container image and pushes it to the Docker Hub repository, using Build and push Docker images.

The `with` key lists a number of input parameters that configures the step:

- context: the build context.
- file: filepath to the Dockerfile.
- push: tells the action to upload the image to a registry after building it.
- tags: tags that specify where to push the image.

> 1. 빌드 머신에 저장소를 체크아웃합니다.
> 2. 도커 로그인과 도커 허브 자격증명을 사용하여 도커 허브에 로그인합니다.
> 3. Docker Setup Builx 액션을 사용하여 BuildKit 빌더 인스턴스를 생성합니다.
> 4. 컨테이너 이미지를 빌드하고 빌드 및 푸시한 도커 이미지를 사용하여 도커 허브 저장소에 푸시합니다.
>
> 포함되는 키값에는 단계를 구성하는 여러개의 입력 파라미터를 목록화합니다.
>
> - context : 빌드 컨텍스트(Dockerfile)
> - file : Dockerfile 경로
> - push : 빌드 후에 레지스트리에 이미지를 업로드하는 과정을 보여줍니다.
> - tags : 이미지를 푸시할때 명세할 태그를 설정합니다.

Add these steps to your workflow file. The full workflow configuration should look as follows:

> 워크 플로우 파일에 여러 단계들을 추가합니다. 전체 워크플로우 설정은 다음과 같습니다.

```yaml
name: ci

on:
  push:
    branches:
      - "main"

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v3
      -
        name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      -
        name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/clockbox:latest
```

### 워크플로우 실행하기(Run the workflow)

Save the workflow file and run the job.

> 워크플로우 파일을 저장하고 작업을 실행합니다.

1. Select Commit changes... and push the changes to the main branch.

After pushing the commit, the workflow starts automatically.

> 1. 커밋 변경사항들을 선택하고 main 브랜치에 변경사항들을 푸시합니다.
>
> 커밋을 푸시한후에 워크플로우는 자동적으로 실행합니다.

2. Go to the Actions tab. It displays the workflow.

Selecting the workflow shows you the breakdown of all the steps.

> 2. Actions 탭으로 이동하세요. 워크플로우가 보일것입니다.
>
> 워크플로우를 선택하는 것은 여러분들에게 모든 단계별 브레이크다운을 보여줍니다.

3. When the workflow is complete, go to your repositories on Docker Hub.

If you see the new repository in that list, it means the GitHub Actions successfully pushed the image to Docker Hub!

> 3. 워크플로우가 완료되면 여러분들의 도커 허브로 이동하세요.
>
> 만약 여러분들이 저장소 목록에 새로운 저장소가 보인다면 GitHub Actions가 성공적으로 도커 허브에 이미지가 푸시된 것입니다.

### 다음단계(Next steps)

In this module, you have learnt how to set up GitHub Actions workflow to an existing Docker project, optimize your
workflow to improve build times and reduce the number of pull requests, and finally, we learnt how to push only specific
versions to Docker Hub. You can also set up nightly tests against the latest tag, test each PR, or do something more
elegant with the tags we are using and make use of the Git tag for the same tag in our image.

You can also consider deploying your application. For detailed instructions, see:

> 이번장에서는, 기존 도커 프로젝트에 GitHub Actions 워크플로우를 설정하는 방법에 대해서 배웠습니다.
> 도커 프로젝트를 빌드 시간을 개선하고 풀 리퀘스트의 개수를 줄이기 위해서 여러분들의 워크 플로우를 최적화합니다.
> 그리고 마지막으로 도커 허브에 특정 버전으로 명세하여 푸시하는 방법에 대해서 배웠습니다.
> 여러분들은 또한 latest 태그에 대한 테스트를 설정하거나 각각의 PR을 테스트하거나, 사용중인 태그로 보다 우아한 작업을 수행하고 이미지의 동일한 태그에 대하여 Git 태그를 사용할 수 있습니다.
>
> 여러분들은 또한 여러분들의 애플리케이션을 배포하는 것을 고려해볼 수 있습니다.
