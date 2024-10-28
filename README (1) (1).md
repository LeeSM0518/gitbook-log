---
description: 일일 회고 32회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.23

{% hint style="success" %}
_**Keep**_

1. 일일 회고를 꾸준히 하면서 성장하기 위해 노력하는 것
{% endhint %}

{% hint style="danger" %}
_**Problem**_

1. 명확한 원인을 파악하지 않고 문제를 해결하는 것
2. 여러 개의 방법 중에 최선의 방법이 아닌 빠른 방법을 선택하는 것
{% endhint %}

{% hint style="warning" %}
_**Try**_

1. 일일 회고를 통해 원인을 명확하게 파악하고 문제를 해결했는지 검토
2. 일일 회고를 통해 최선의 방법을 선택했는지 검토
{% endhint %}

## 오늘 할 일

* [x] 회사 업무
  * [x] AI 분석 실패 해결
* [x] 사이드 프로젝트
  * [x] 테스트 CI 구축
  * [x] 개발 서버 CI/CD 구축
* [x] 개인 학습
  * [x] 자료구조 학습&#x20;
    * [x] Stack
    * [x] Queue
    * [x] Hash
    * [x] Tree

## 경험 및 배움

### 회사 업무

#### AI 분석 실패 해결

특정 AI 모델들에 대해 분석이 실패하여 원인 분석과 문제 해결을 진행했다.

먼저 원인 분석을 진행했으며 어느 테스트 서버에 문제가 발생한 것인지 확인하고, 해당 서버의 분석 데이터를 검토했다. 분석 요청 데이터가 저장되어 있지 않은 것을 발견했고, 저장되어 있지 않은 이유가 AI 모델 데이터가 존재하지 않아서 해당 AI 모델로 요청이 수신됐을 때 처리를 실패한 것으로 판단됐다. 원인 분석을 완료하고 문제가 발생했던 AI 모델을 데이터베이스에 저장하고 AI 분석을 재시도 해보니 정상적으로 분석이 완료되어 데이터가 저장된 것을 확인할 수 있었다.

테스트 엔지니어나 백엔드 엔지니어가 AI 분석 실패 원인을 빠르게 파악할 수 있도록, AI 분석 실패시 AI 모델이 존재하지 않아 실패했다는 것을 에러 메시지로 띄우는 것이 좋을 것 같다.



### 사이드 프로젝트

#### 테스트 CI 구축

이전에 진행하던 테스트 CI 구축 작업을 이어서 진행했다.

이전에 Gradle 빌드시 테스트를 실패하여 원인 분석을 마치지 못 한 상태에서 마무리가 되어 해당 작업을 먼저 진행했다. Github Actions에 출력되는 결과만 봐서는 에러 확인이 어려우므로 Actions에 테스트 결과 파일을 업로드 시키는 작업을 다음과 같이 추가했다.

```yaml
- name: Upload Test Reports
  if: always()
  uses: actions/upload-artifact@v3
  with:
    name: test-reports
    path: build/reports/tests/test
```

그 후 테스트를 다시 시도해보니 실패한 원인을 곧바로 파악할 수 있었다. 실패하는 테스트를 해결한 후에 재배포 하니 정상 동작하는 것을 확인할 수 있었다. 최종 코드는 다음과 같으며 Gradle 정보를 캐싱하고 Gradle 빌드를 병렬로 처리하는 옵션을 추가하여 테스트 CI 성능을 개선하는 작업도 추후에 진행할 예정이다.

```yaml
name: Build Test

on:
  pull_request:
    types: [ opened, reopened, synchronize ]
    branches: [ develop, main ]

env:
  CONTAINER_CONTEXT_PATH: 'docker'
  DOCKERFILE_PATH: 'docker/Dockerfile'

jobs:
  gradle-and-docker-build-test:
    runs-on: ubuntu-latest
    timeout-minutes: 30

    steps:
      - name: Checkout sources
        uses: actions/checkout@v4

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Build with Gradle
        run: ./gradlew build

      - name: Upload Test Reports
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: test-reports
          path: build/reports/tests/test

      - name: Build the Docker image
        run: docker build ${{ env.CONTAINER_CONTEXT_PATH }} --file ${{ env.DOCKERFILE_PATH }}
```



#### 개발 서버 CI/CD 구축

테스트 CI 구축을 마친 후에 이어서 AWS에 개발 서버를 배포하는 CI/CD 구축 작업을 진행했다.

GitHub Actions와 AWS를 활용하여 CI/CD 를 구축하는 글 들을 보니 주로 AWS CodeDeploy와 AWS S3를 활용하거나 Dockerhub에 이미지를 등록하여 AWS에 SSH로 접근해 Docker 이미지를 Pull & Run 하는 글 들이 많았다. 하지만 나는 개발 서버에 CodeDeploy와 S3를 사용해 AWS의 추가 요금을 내고 싶지 않았으며, 공개되면 안되는 정보들이 서버에 존재할 수 있으므로 Dockerhub로 공개적으로 올리는 것을 원치 않았다. Dockerhub에 private 저장소도 있지만 해당 저장소는 무료로 한 개만 만들 수 있으므로, 추후에 운영 서버나 추가로 서버를 배포할 경우 유료가 되어 이를 사용하지 않는 것으로 결정했다.

위와 같은 이유로 scp를 활용해 Docker 이미지를 직접 AWS로 복사하고, EC2에 접근하여 Docker 이미지를 로드한 후에 docker-compose로 실행하도록 구축하는 것을 진행했다. 먼저 scp가 정상적으로 동작하는지 테스트 해보기 위해 다음과 같이 Actions를 작성한 후에 배포해봤다.

```yaml
name: Publish Develop Server

on:
  push:
    branches: [ develop ]
  workflow_dispatch:

env:
  CONTAINER_CONTEXT_PATH: 'docker'
  DOCKERFILE_PATH: 'docker/Dockerfile'
  IMAGE_NAME: 'develop-devooks-backend'
  EC2_SSH_KEY: ${{ secrets.DEVELOP_EC2_SSH_PRIVATE_KEY }}
  EC2_USER: ${{ secrets.DEVELOP_EC2_SSH_USER }}
  EC2_HOST: ${{ secrets.DEVELOP_EC2_HOST }}

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 30

    steps:
      - name: Checkout sources
        uses: actions/checkout@v4

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Build with Gradle
        run: ./gradlew build -x test

      - name: Upload Test Reports
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: test-reports
          path: build/reports/tests/test

      - name: Build the Docker image
        run: docker build ${{ env.CONTAINER_CONTEXT_PATH }} --file ${{ env.DOCKERFILE_PATH }} --tag ${{ env.IMAGE_NAME }}:latest

      - name: Save Docker image as tar file
        run: docker save ${{ env.IMAGE_NAME }}:latest -o ${{ env.IMAGE_NAME }}.tar

      - name: Transfer Docker tar to EC2
        run: |
          echo "$EC2_SSH_KEY" > ec2_key.pem
          chmod 600 ec2_key.pem
          scp -i ec2_key.pem -o StrcitHostKeyChecking=no ${{ env.IMAGE_NAME }}.tar $EC2_USER@EC2_HOST:/home/$EC2_USER/

      - name: Deploy to EC2
        uses: appleboy/ssh-action@master
        with:
          host: ${{ env.EC2_HOST }}
          username: ${{ env.EC2_USER }}
          key: ${{ env.EC2_SSH_KEY }}
          script: |
            docker load -i /home/$EC2_USER/${{ env.IMAGE_NAME}}.tar

```



배포하여 확인해보니 scp를 실행하는 파트에서 "command-line: line 0: Bad configuration option: strcithostkeychecking" 에러가 발생하여, scp가 불가능 한 것인지를 알아보고 이를 해결하는 작업을 진행할 예정이다.



### 개인 학습

#### 자료구조 학습

코딩 테스트를 준비하기 위해 자료구조를 문서로 정리해보면서 해당 자료구조에 대한 문제를 하나씩 풀어봤다.

이번에 학습한 것은 Stack과 Queue, Hash, Tree 이다. 해당 자료구조들은 이해하기 쉽고 자주 사용해 본 자료구조이므로 내용을 간단하게만 정리하고 문제를 풀어보는 것을 위주로 진행했다. 해당 자료구조들을 정리한 문서들은 다음과 같다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/data-structure/stack" %}

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/data-structure/queue" %}

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/data-structure/hash" %}

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/data-structure/tree" %}





## 앞으로 할 일

* [ ] 회사 업무
  * [ ] 변화 탐지 영역 조회 API 성능 측정
* [ ] 사이드 프로젝트
  * [ ] CI/CD 구축
  * [ ] 테스트 CI 성능 개선
