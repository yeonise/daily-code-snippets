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

