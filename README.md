---
description: 일일 회고 6회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 😥 2024.07.30

## 할일 및 한일

* [x] 회사 업무
  * [x] 영상 입력 실패 해결 코드 수정
  * [x] Github Actions 개선
* [x] 개인 공부
  * [x] @Transactional 학습

## 경험 및 배움

### 회사 업무

#### 영상 입력 실패 해결 코드 수정

영상 입력을 실패하는 부분을 라이브러리를 사용하는 것이 아니라 PostGIS를 사용하여 intersection 하도록 수정했었다. 이에 대해 팀장님은 해당 방법은 마지막으로 사용해야 할 방법이라고 하셨다.

팀장님이 기관에 출장가셨을 때 에러 로그를 분석하여 발견한 것은 영상과 관심지역에 대해 intersection이 두 번 이루어지는데 두 번째 intersection을 호출할 때 에러가 발생한다고 하셨다. 그리고 첫 번째 intersection에 대한 결과와 두 번째 intersection에 대한 결과가 동일한 것 같다고 하시며 두 번째 intersection을 지운 후에 기관에 반영해서 테스트를 해보자고 하셨다.

위와 같은 이유로 PostGIS로 intersection 하는 코드에 대한 PR은 대기시킨 후에 두 번째 intersection 코드를 지운 후에 도커 이미지를 생성하고 해당 이미지를 사내 서버에 배포하여 테스트를 완료했다. 그 후 기관 장기 출장을 간 팀원에게 해당 이미지와 기관에서 테스트를 하는 방법을 전달드리면서 영상 입력 테스트를 요청했다.

어제 기관에 출장가서 에러에 대한 정확한 원인 파악을 못 한 것이 아쉬웠다. 다음에 는 기관에서 작업할 수 있는 한정적인 시간을 계획적으로 사용하여 원인 파악을 우선시 해야될 것 같다.

#### Github Actions 개선

저번주에 마무리 하지 못 한 Github Actions를 개선하는 작업을 이어서 진행했다.

gradle build를 Github Runner로 실행하도록 하고 docker build와 push만 self-hosted Runner를 사용하도록 변경하니 3개의 Repository에서 CI/CD를 성공하는 것을 확인했다.

개선 작업을 진행하면서 다음과 같은 에러를 해결했다.

1. 큰 용량의 파일에 대한 아티팩트 업로드 실패 발생
2. Dockerfile을 찾지 못 하는 에러 발생

첫 번째 문제는 캐시를 사용하여 해결을 했다. jar 파일을 Job과 Job 사이에서 옮길 때 아티팩트 업로드와 다운로드를 사용했는데 이때 다음과 같은 에러가 발생했다.

```
Error: aborting artifact upload
Total size of all the files uploaded is 92274688 bytes
File upload process has finished. Finalizing the artifact upload
Upload finished. There were 1 items that failed to upload

The raw size of all the files that were specified for upload is 106311589 bytes
The size of all the files that were uploaded is 92274688 bytes. This takes into account any gzip compression used to reduce the upload size, time and storage

Note: The size of downloaded zips can differ significantly from the reported size. For more information see: https://github.com/actions/upload-artifact#zipped-artifact-downloads 

Error: An error was encountered when uploading build-artifacts. There were 1 items that failed to upload.
```

확인해보니 큰 파일을 아티팩트로 올리면 에러가 발생할 수 있으며 아티팩트는 제한 용량이 캐시보다 작은 것을 알 수 있었다. 그래서 Job끼리 jar 파일을 주고 받을 때 아티팩트가 아닌 캐시를 사용하도록 수정한 후에 Actions를 실행해보니 정상적으로 동작했다.

두 번째 문제는 Actions에 actions/checkout@v3 를 추가하여 해결했다. 확인해보니 checkout을 하지 않아서 Dockerfile에 접근할 수 없었던 것이이였다.

Github Actions를 수정해야할 Repository가 아직 7개가 남아있으나 버그 수정작업을 우선적으로 진행해야 하므로 다음주에 이어서 작업할 예정이다.



### 개인 공부

#### @Transactional 학습

다음 글에 @Transactional을 학습한 내용을 정리했다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-tech/spring/transactional" %}

해당 글은 @Transactional 에 대한 기본 개념과 사용하는 방법 및 주의해야할 점이 정리되어 있다. 참고한 글을 정리하면서 @Transactional 사용 방법에 대해 다시 한번 상기시킬 수 있어서 좋았으나 자세한 동작 원리가 설명되어 있지 않아서 동작 원리에 대해 정리된 글을 추가로 정리해 볼 예정이다.

{% embed url="https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth" %}
@Transactional 동작 원리에 관련된 글
{% endembed %}



## 개선 및 목표

* [ ] 회사 업무
  * [ ] 700만 개의 라벨 데이터가 존재하는 경우의 슬로우 쿼리 해결
  * [ ] 인퍼런스 결과 저장 실패 해결
  * [ ] 백엔드 Github Actions 개선
* [ ] 개인 학습
  * [x] @Transactional 학습
  * [ ] AOP의 Joinpoint 학습
  * [ ] @Transactional 동작원리 학습
