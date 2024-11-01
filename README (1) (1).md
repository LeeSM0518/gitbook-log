---
description: 일일 회고 37회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.29

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

## 경험 및 배움

### 회사 업무

#### 영상 조회와 변화 탐지 영역 조회 버그 분석

프론트 측에서 영상 조회의 속도가 느리다하여 영상 조회 서버의 개수를 늘려 성능을 향상시켰다. 하지만 여전히 특정 조회 요청들이 Pending 상태로 멈춰있는 현상이 발생하여 해당 현상이 발생하는 요청 값을 전달받았다. **특정 값으로 요청을 했을 때 Pending이 발생하는 것인지 아니면 여전히 성능이 부족하여 Pending이 발생하는 것인지를 분석**할 예정이다.

그 다음으로 프론트 측에서 변화 탐지 영역을 조회할 때 에러가 발생한다고 전달받았다. 요청 값을 확인해보니 전세계를 포함하는 영역을 조회 파라미터로 전달할 경우 에러가 발생했다. 에러 로그를 확인했을 때는 MultiPolygon으로 변환하는 로직에서 예외가 발생한 것으로 확인됐다. **쿼리로 조회된 결과를 MultiPolygon으로 변환해서 발생하는 에러로 추측**되지만 정확하게 어디서 어떻게 에러가 발생하는 것인지 분석할 예정이다.



### 사이드 프로젝트

#### CI/CD 구축

이전에 진행했던 개발 서버 CI/CD 구축 작업을 이어서 진행했다. 이전에는 CI/CD를 추가 비용 없이 구축하기 위해 Dockerhub와 AWS S3, AWS CodeDeploy를 사용하지 않고 scp를 활용해 서버를 배포하려 했다. 하지만 GitHub Actions애서 **scp를 활용하여 Docker 이미지를 전송할 때 매우 오래 걸리는 것으로 확인**됐다. scp로 전송하는 것은 불가능할 것으로 판단되어 **Dockerhub의 private repository를 활용하는 것으로 결정**했다.&#x20;

이 방법을 이전에 선택하지 않은 이유는 무료로 private repository를 사용할 수 있는 개수는 1개이므로 개발 서버와 운영 서버를 나누게 되었을 때 비용이 발생하기 때문이였다. 이를 잘 활용할 수 있는 방법을 고민해보다가 도커 이미지의 **태그를 개발 서버와 운영 서버를 다르게 구성**하는 방법을 생각해냈다. 이로써 개발 서버는 도커 이미지의 태그에 develop을 붙이고 운영 서버는 operation을 붙여서 배포하는 것으로 결정했다.

먼저 AWS EC2에 docker와 docker-compose 를 설치하고 GitHub Actions로 실행할 docker-compose.yml 파일을 작성해놨다. 그 후 GitHub Actions로 CI/CD workflow를 작성해서 배포해보니 정상 동작하는 것을 확인했다. CI/CD workflow 파일은 다음과 같다.

```yaml
name: Publish Develop Server

on:
  push:
    branches: [ develop ]
  workflow_dispatch:

env:
  CONTAINER_CONTEXT_PATH: 'docker'
  DOCKERFILE_PATH: 'docker/Dockerfile'
  IMAGE_NAME: 'devooks-backend'
  IMAGE_TAG: 'develop'
  EC2_SSH_KEY: ${{ secrets.DEVELOP_EC2_SSH_PRIVATE_KEY }}
  EC2_USER: ${{ secrets.DEVELOP_EC2_SSH_USER }}
  EC2_HOST: ${{ secrets.DEVELOP_EC2_HOST }}
  DOCKER_HUB_USERNAME: ${{ secrets.DOCKER_HUB_USERNAME }}
  DOCKER_HUB_PASSWORD: ${{ secrets.DOCKER_HUB_PASSWORD }}
  APPLICATION_YAML: ${{ secrets.APPLICATION_YAML }}

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 30

    steps:
      - name: Checkout sources
        uses: actions/checkout@v4

      - name: Create application.yaml
        run: |
          touch ./src/main/resources/application.yml
          echo "${{ env.APPLICATION_YAML }}" > ./src/main/resources/application.yml

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Build with Gradle
        run: ./gradlew build --parallel

      - name: Upload Test Reports
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: test-reports
          path: build/reports/tests/test

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ env.DOCKER_HUB_USERNAME }}
          password: ${{ env.DOCKER_HUB_PASSWORD }}

      - name: Get current date and time in custom format
        run: |
          CURRENT_DATE_TIME=$(date "+%Y%m%d%H%M%S")
          echo "Current date and time is $CURRENT_DATE_TIME"
          echo "CURRENT_DATE_TIME=$CURRENT_DATE_TIME" >> $GITHUB_ENV

      - name: Use current date and time
        run: echo "The date and time in custom format is ${{ env.CURRENT_DATE_TIME }}"

      - name: Build and Push the Docker image and
        uses: docker/build-push-action@v6
        with:
          context: ${{ env.CONTAINER_CONTEXT_PATH }}
          file: ${{ env.DOCKERFILE_PATH }}
          tags: ${{ env.DOCKER_HUB_USERNAME}}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_TAG }}-${{ env.CURRENT_DATE_TIME }}
          push: true

      - name: Deploy to EC2
        uses: appleboy/ssh-action@master
        with:
          host: ${{ env.EC2_HOST }}
          username: ${{ env.EC2_USER }}
          key: ${{ env.EC2_SSH_KEY }}
          script: |
            sudo docker login -u "${{ env.DOCKER_HUB_USERNAME }}" -p "${{ env.DOCKER_HUB_PASSWORD }}"
            echo "DEVELOP_DEVOOKS_TAG=${{ env.IMAGE_TAG }}-${{ env.CURRENT_DATE_TIME }}" > .env
            sudo docker-compose down && sudo docker-compose up -d
```



## 해야 할 일

* [ ] 회사 업무
  * [ ] 영상 조회 버그 해결
  * [ ] 변화 탐지 영역 조회 버그 해결
  * [ ] 변화 탐지 영역 조회 API 성능 측정
  * [ ] GDAL 테스트 환경 개선
* [ ] 사이드 프로젝트
  * [x] CI/CD 구축
  * [ ] 테스트 CI 성능 개선
