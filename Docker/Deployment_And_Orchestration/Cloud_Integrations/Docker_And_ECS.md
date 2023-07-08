## Deploying Docker containers on ECS

### 개요(Overview)

The Docker Compose CLI enables developers to use native Docker commands to run applications in Amazon Elastic Container
Service (ECS) when building cloud-native applications.

> 개발자는 Docker Compose CLI를 사용하여 클라우드 네이티브 애플리케이션을 빌드할때 ECS에 있는 애플리케이션을 실행하기 위해서 네이티브 도커 명령어를 실행할 수 있습니다.

The integration between Docker and Amazon ECS allows developers to use the Docker Compose CLI to:

- Set up an AWS context in one Docker command, allowing you to switch from a local context to a cloud context and run
  applications quickly and easily
- Simplify multi-container application development on Amazon ECS using Compose files

> Docker와 Amazon ECS간에 통합은 Docker Compose CLI를 사용하는 것을 개발자에게 허용합니다.
>
> - 로컬 컨텍스트에서 클라우드 컨텍스트로 변경하는 것을 허용하고 애플리케이션을 빠르고 쉽게 실행하는 단일 도커 명령어에 AWS 컨텍스트 설정합니다.
> - Compose 파일을 사용하여 ECS에서 멀티 컨테이너 애플리케이션 개발을 간단하게 합니다.

Also see the [ECS integration architecture](https://docs.docker.com/cloud/ecs-architecture/),
[full list of compose features](https://docs.docker.com/cloud/ecs-compose-features/)
and [Compose examples for ECS integration.](https://docs.docker.com/cloud/ecs-compose-examples/)

> 또한 ECS 통합 아키텍처, 컴포증 기능 목록, ECS 통합을 위한 컴포즈 예제를 참고해주세요.

### 선행조건(Prerequisites)

To deploy Docker containers on ECS, you must meet the following requirements:

> ECS에서 도커 컨테이너들에 배포하기 위해서는 여러분들은 다음 요구조건들을 만족해야 합니다.

1. Download and install the latest version of Docker Desktop.

- [Download for Mac](https://docs.docker.com/desktop/install/mac-install/)
- [Download for Windows](https://docs.docker.com/desktop/install/windows-install/)

> 1. 도커 데스크톱의 최신 버전을 다운로드하고 설치하세요.

2. Ensure you have an AWS account.

> 2. AWS 게정을 가지고 있어야 합니다.

Docker not only runs multi-container applications locally, but also enables developers to seamlessly deploy Docker
containers on Amazon ECS using a Compose file with the `docker compose up` command. The following sections contain
instructions on how to deploy your Compose application on Amazon ECS.

> 도커는 멀티 컨테이너 애플리케이션을 로컬에서 실행할 뿐만 아니라 개발자들이 `docker compose up` 명령어를 가진 Compose 파일을 사용하여
> 도커 컨테이너를 Amazon ECS에 원할하게 배포할 수 있도록 합니다.
> 다음 장은 Amazon ECS에 Compose 애플리케이션을 배포하는 방법에 대한 명령어들이 포함됩니다.

### ECS에 애플리케이션을 실행(Run an application on ECS)

#### 요구조건(Requirements)

AWS uses a fine-grained permission model, with specific role for each resource type and operation.

> AWS는 각각의 리소스 타입과 작업에 대한 특정 역할과 함께 세부적인 권한 모델을 사용합니다.

To ensure that Docker ECS integration is allowed to manage resources for your Compose application, you have to ensure
your AWS
credentials [grant access to following AWS IAM permissions](https://aws.amazon.com/ko/iam/features/manage-permissions/):

> 도커 ECS 통합은 여러분들의 Compose 애플리케이션의 리소스를 관리할 수 있도록 하려면, AWS 자격증명이 다음 AWS IAM 권한에 대한 접근 권한이 부여하는지 확인해야 합니다.

- application-autoscaling:*
- cloudformation:*
- ec2:AuthorizeSecurityGroupIngress
- ec2:CreateSecurityGroup
- ec2:CreateTags
- ec2:DeleteSecurityGroup
- ec2:DescribeRouteTables
- ec2:DescribeSecurityGroups
- ec2:DescribeSubnets
- ec2:DescribeVpcs
- ec2:RevokeSecurityGroupIngress
- ecs:CreateCluster
- ecs:CreateService
- ecs:DeleteCluster
- ecs:DeleteService
- ecs:DeregisterTaskDefinition
- ecs:DescribeClusters
- ecs:DescribeServices
- ecs:DescribeTasks
- ecs:ListAccountSettings
- ecs:ListTasks
- ecs:RegisterTaskDefinition
- ecs:UpdateService
- elasticloadbalancing:*
- iam:AttachRolePolicy
- iam:CreateRole
- iam:DeleteRole
- iam:DetachRolePolicy
- iam:PassRole
- logs:CreateLogGroup
- logs:DeleteLogGroup
- logs:DescribeLogGroups
- logs:FilterLogEvents
- route53:CreateHostedZone
- route53:DeleteHostedZone
- route53:GetHealthCheck
- route53:GetHostedZone
- route53:ListHostedZonesByName
- servicediscovery:*

GPU support, which relies on EC2 instances to run containers with attached GPU devices, require a few additional
permissions:

> EC2 인스턴스를 사용하여 GPU 장치가 연결된 컨테이너를 실행하는 GPU 지원에는 몇가지 추가 권한이 필요합니다.

- ec2:DescribeVpcs
- autoscaling:*
- iam:CreateInstanceProfile
- iam:AddRoleToInstanceProfile
- iam:RemoveRoleFromInstanceProfile
- iam:DeleteInstanceProfile

### AWS 컨텍스트 생성하기(Create AWS context)

Run the `docker context create ecs myecscontext` command to create an Amazon ECS Docker context named myecscontext. If
you have already installed and configured the AWS CLI, the setup command lets you select an existing AWS profile to
connect
to Amazon. Otherwise, you can create a new profile by passing an `AWS access key ID and a secret access key`. Finally,
you can configure your ECS context to retrieve AWS credentials by `AWS_*` environment variables, which is a common way
to
integrate with third-party tools and single-sign-on providers.

> myecscontext라는 이름을 가진 Amazon ECS Docker context를 생성하기 위해서 `docker context create ecs myecscontext`를 실행하세요.
> 만약 여러분들이 이미 AWS CLI를 설치하고 설정하였다면 설정 명령을 사용하여 Amazon과 연결할 존재하는 AWS 프로파일을 선택할 수 있습니다.
> 그렇지 않으면 여러분들은 AWS 액세스 키 아이디와 시크릿 접근 키를 전달함으로써 새로운 프로파일을 생성할 수 있습니다.
> 마지막으로 서드 파티 툴 및 Single-sign-on 공급자와 통합하는 일반적인 방법인 `AWS_*` 환경 변수를 통해 AWS 자격 증명을 검색하도록 ECS 컨텍스트를 구성할 수 있습니다.

```
? Create a Docker context using:  [Use arrows to move, type to filter]
  An existing AWS profile
  AWS secret and token credentials
> AWS environment variables
```

After you have created an AWS context, you can list your Docker contexts by running the docker context ls command:

> AWS 컨텍스트를 생성한후에 여러분들은 `docker context ls` 명령어를 실행하여 도커 컨텍스트를 볼수 있습니다.

```
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT   ORCHESTRATOR
myecscontext        ecs                 credentials read from environment
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                         swarm
```

### Compose 애플리케이션 실행하기(Run a Compose application)

You can deploy and manage multi-container applications defined in Compose files to Amazon ECS using the `docker compose`
command. To do this:

> 여러분들은 `docker compose` 명령어를 사용하여 Amazon ECS에 있는 Compose 파일에 정의된 멀티 컨테이너 애플리케이션들을 배포하고 관리할 수 있습니다.

- Ensure you are using your ECS context. You can do this either by specifying the `--context myecscontext` flag with
  your
  command, or by setting the current context using the command `docker context use myecscontext`.
- Run `docker compose up` and `docker compose down` to start and then stop a full Compose application.

> - ECS 컨텍스트를 사용중인지 확인합니다. 여러분들은 명령어로 `--context myecscontext` 옵션을 지정하여 이 작업을 수행할 수도 있습니다.
    > 또는 `docker context use myecscontext` 명령어를 사용하여 현재 컨텍스트를 설정할 수 있습니다.
> - 전체 Compose 애플리케이션을 `docker compose up`으로 실행하고 `docker compose down`으로 중지합니다.

By default, `docker compose up` uses the `compose.yaml` or `docker-compose.yaml` file in the current folder. You can
specify
the working directory using the --workdir flag or specify the Compose file directly using `docker compose --file
mycomposefile.yaml up`.

> 기본적으로 `docker compose up` 명령어는 현재 폴더에 있는 `compose.yaml` 또는 `docker-compose.yaml` 파일을 사용합니다.
> 여러분들은 docker comopse up 명령어 수행시 --workdir 옵션을 사용하여 작업 디렉토리를 명세할 수 있습니다.
> 또는 `docker compose --file mycomposefile.yaml up` 명령어를 사용하여 Compose 파일을 직접 명세하여 실행할 수 있습니다.

You can also specify a name for the Compose application using the `--project-name` flag during deployment. If no name is
specified, a name will be derived from the working directory.

> 여러분들은 개발동안 `--project-name` 옵션을 사용하여 Compose 애플리케이션에 대한 이름을 명세할 수 있습니다.
> 만약 이름을 명세하지 않는다면 이름은 작업 디렉토리로부터 추론될 것입니다.

Docker ECS integration converts the Compose application model into a set of AWS resources, described as
a [CloudFormation template](https://aws.amazon.com/ko/cloudformation/). The actual mapping is described
in [technical documentation](https://github.com/docker/compose-cli/blob/main/docs/ecs-architecture.md).
You can review the generated template using `docker compose convert` command, and follow CloudFormation applying this
model within AWS web console when you run `docker compose up`, in addition to CloudFormation events being displayed in
your terminal.

> Docker ECS 통합은 Compose 애플리케이션 모델을 CloudFormation이라고 하는 AWS 리소스 세트로 변환합니다.
> 실제 매핑은 기술 문서에 설명되어 있습니다. 여러분들은 `docker compose convert` 명령어를 사용하여 생성된 템플릿을 리뷰할 수 있습니다.
> 그리고 터미널에 CloudFormation 이벤트들이 출력되는거에 더하여 `docker compose up`을 수행할때 AWS 웹 콘솔 내에서 이 모델이 적용하는 CloudFormation을 따를 수 있습니다.

- You can view services created for the Compose application on Amazon ECS and their state using the `docker compose ps`
  command.
- You can view logs from containers that are part of the Compose application using the `docker compose logs` command.

> - Amazon ECS에 Compose 애플리케이션을 위해 생성된 서비스들을 볼수 있고 `docker compose ps` 명령어를 사용하여 서비스들의 상태를 볼 수 있습니다.
> - `docker compose logs` 명령어를 사용하여 Compose 애플리케이션의 일부인 컨테이너에서 로그를 볼수 있습니다.

Also see the [full list of compose features](https://docs.docker.com/cloud/ecs-compose-features/).

> compose 기능들의 전체 목록을 참고해주세요.

### Rolling update

To update your application without interrupting production flow you can simply use `docker compose up` on the updated
Compose project. Your ECS services are created with rolling update configuration. As you run `docker compose up` with a
modified Compose file, the stack will be updated to reflect changes, and if required, some services will be replaced.
This replacement process will follow the rolling-update configuration set by your services `deploy.update_config`
configuration.

> 프로덕션 흐름을 중단하지 않고 애플리케이션을 업데이트 하기 위해서는 `docker compose up`을 사용하여 업데이트된 컴포즈 프로젝트에서 할수 있습니다.
> 여러분들의 ECS 서비스들은 롤링 업데이트 설정을 가지고 생성되었습니다. 수정된 컴포즈 파일로 `docker compose up`을 실행할때
> 애플리케이션 스택은 변경 사항을 반영할 것입니다. 그리고 요구된다면 몇몇 서비스들은 대체될 것입니다.
> 이 대체 프로세스들은 `deploy.update_config` 구성으로 설정된 롤링 업데이트 설정을 따릅니다.

AWS ECS uses a percent-based model to define the number of containers to be run or shut down during a rolling update.
The Docker Compose CLI computes rolling update configuration according to the `parallelism` and `replicas` fields.
However,
you might prefer to directly configure a rolling update using the extension fields `x-aws-min_percent` and
`x-aws-max_percent`. The former sets the minimum percent of containers to run for service, and the latter sets the
maximum
percent of additional containers to start before previous versions are removed.

> AWS ECS는 롤릴 업데이트 동안 실행하거나 셧다운 시켜야할 컨테이너의 개수를 정의하기 위해서 퍼센트 기반 모델을 사용합니다.
> Docker Compose CLI는 롤링 업데이트 설정을 병행성(parallelism)과 복제(replicas) 필드들에 따라 계산합니다.
> 그러나 여러분들은 연장 필드인 `x-aws-min_percent`와 `x-aws-max_percent`를 사용하여 롤링 업데이트를 직접적으로 사용할 수 있습니다.
> 전자는 서비스를 위해서 컨테이너의 최소 백분율을 설정하고, 후자는 이전 버전이 제거되기 전에 시작할 추가 컨테이너의 최대 백분율을 설정합니다.

By default, the ECS rolling update is set to run twice the number of containers for a service (200%), and has the
ability to shut down 100% containers during the update.

> 기본적으로 ECS 롤링 업데이트는 서비스에 대한 컨테이너 수(200%)의 두배로 실행되도록 설정되며, 업데이트 중에 100% 컨테이너를 종료할 수 있습니다.

### 애플리케이션 로그 보기(View application logs)

The Docker Compose CLI configures AWS CloudWatch Logs service for your containers. By default you can see logs of your
compose application the same way you check logs of local deployments:

> Docker Compose CLI는 여러분들의 컨테이너를 대상으로 AWS CloudWatch Logs를 설정합니다.
> 기본적으로 여러분들은 로컬 배포들의 로그를 확인하는 방법으로 컴포즈 애플리케이션의 로그를 볼 수 있습니다.

```mysql
# fetch logs for application in current working directory
$ docker compose logs

# specify compose project name
$ docker compose --project-name PROJECT logs

# specify compose file
$ docker compose --file /path/to/docker-compose.yaml logs
```

A log group is created for the application as `docker-compose/<application_name>`, and log streams are created for each
service and container in your application as `<application_name>/<service_name>/<container_ID>`.

> `docker-compose/<application_name>`으로서 애플리케이션에 대하여 로그 그룹을 생성합니다.
> 그리고 로그 스트림들은 여러분들의 애플리케이션에 `<application_name>/<service_name>/<container_ID>`로서 각각에 서비스와 컨테이너에 대하여 생성됩니다.

You can fine tune AWS CloudWatch Logs using extension field `x-aws-logs_retention` in your Compose file to set the
number
of retention days for log events. The default behavior is to keep logs forever.

> 여러분들의 컴포즈 파일에서 확장 필드인 x-aws-logs_retention을 사용하여 AWS CloudWatch 로그를 미세조정하여 로그 이벤트의 보존 일수를 설정할 수 있습니다.
> 기본적인 수행은 로그를 영원히 보관합니다.

You can also pass `awslogs` parameters to your container as standard Compose file `logging.driver_opts` elements.
See [AWS documentation](https://docs.amazonaws.cn/en_us/AmazonECS/latest/developerguide/using_awslogs.html) for details
on available log driver options.

> 여러분들은 또한 `awslogs` 파라미터를 여러분들의 컨테이너에 표준 컴포즈 파일 'logging.driver_opts` 요소로서 전달할 수 있습니다.
> 사용가능한 로그 드라이버 옵션들에 대한 자세한 정보는 AWS 문서를 참고해주세요.

### 개인 도커 이미지(Private Docker images)

The Docker Compose CLI automatically configures authorization so you can pull private images from the Amazon ECR
registry on the same AWS account. To pull private images from another registry, including Docker Hub, you’ll have to
create a Username + Password (or a Username + Token) secret on
the [AWS Secrets Manager service](https://docs.aws.amazon.com/secretsmanager/).

> 도커 컴포즈 CLI는 동일한 AWS 게정의 Amazon ECR 레지스트리에서 개인 이미지를 가져올 수 있도록 자동으로 인증을 설정합니다.
> 도커 허브를 포함한 또다른 레지스트리로부터 개인 이미지들을 가져오기 위해서는 여러분들은 AWS Secrets Manager Service에서
> Username + Password(또는 Username + Token) 시크릿을 생성해야합니다.

For your convenience, the Docker Compose CLI offers the `docker secret` command, so you can manage secrets created on
AWS
SMS without having to install the AWS CLI.

> 여러분들의 편리함을 위해서 Docker Compose CLI는 도커 시크릿 명령어를 제공합니다.
> AWS CLI를 설치하지 않고도 AWS SMS에서 생성된 시크릿을 관리할 수 있습니다.

First, create a `token.json` file to define your DockerHub username and access token.

> 첫번째로 여러분들의 도커 허브 username과 access token을 정의하기 위해서 token.json 파일을 생성하세요.

For instructions on how to generate access tokens,
see [Managing access tokens](https://docs.docker.com/docker-hub/access-tokens/).

> access 토큰을 생성하는 방법에 대한 명령어는 access 토큰 관리하기를 참고해주세요.

```
{
  "username":"DockerHubUserName",
  "password":"DockerHubAccessToken"
}
```

You can then create a secret from this file using `docker secret`:

> `docker secret`을 사용하여 이 파일로부터 시크릿을 생성할 수 있습니다.

```shell
$ docker secret create dockerhubAccessToken token.json
arn:aws:secretsmanager:eu-west-3:12345:secret:DockerHubAccessToken
```

Once created, you can use this ARN in your Compose file using x-aws-pull_credentials custom extension with the Docker
image URI for your service.

> 생성된 후에는 서비스에 대한 도커 이미지 URI와 함께 x-aws-pull_credentials 사용자 지정 확장자를 사용하여 이 ARN을 컴포즈 파일에서 사용할 수 있습니다.

```yaml
services:
  worker:
    image: mycompany/privateimage
    x-aws-pull_credentials: "arn:aws:secretsmanager:eu-west-3:12345:secret:DockerHubAccessToken"
```

```
Note

If you set the Compose file version to 3.8 or later, you can use the same Compose file for local deployment 
using docker-compose. Custom ECS extensions will be ignored in this case.
```

> 참고
>
> 만약 여러분들이 3.8 이후 버전의 컴포즈 파일을 설정하였다면 여러분들은 docker-compose를 사용하여 로컬 개발을 위해서 같은 컴포즈 파일을 사용할 수 있습니다.
> 커스텀 ECS 확장자들은 이 경우에는 무시될 것입니다.
