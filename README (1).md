---
description: 일일 회고 1회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 😊 2024.07.24

## 할일 및 한일

* [x] 회사 업무
  * [x] PR 템플릿 수정
  * [x] 신기능 프로세스 설계
  * [x] CI/CD 버그 해결
* [x] 개인 공부
  * [x] Spring Event 학습



## 경험 및 배움

### 회사 업무

#### PR 템플릿 수정

기존에는 다음과 같이 요구사항과 작업 종류, 코드 리뷰 시 참고 사항, 테스트 항목이 존재했었다.

```markdown
# 요구사항 및 기능 요약

# 관련 자료

# 작업 종류
- [ ] 기능 추가
- [ ] 기능 변경
- [ ] 버그 수정
- [ ] 의존성, 환경 변수, 빌드 관련 코드 업데이트
- [ ] 기타 사소한 수정

# 코드 리뷰 시 참고 사항

# 테스트
## 테스트 방법
- [ ] 테스트 코드 작성 (단위, 통합)
- [ ] 별도의 코드 작성하여 테스트 (커밋 X)
- [ ] 배포 후 브라우저에서 확인
- [ ] 배포 후 DB 데이터 확인

## 테스트 입력 데이터
## 테스트 결과
```



이렇게 구성해서 사용해보니 작성된 요구사항 항목 외에는 다른 건 제대로 작성되지 않으며 리뷰어가 딱히 확인하지 않았다. 작업 종류는 PR 제목에 붙여지는 feat이나 fix, bug로 확인할 수 있었으며 테스트는 PR을 올리는 사람에게 중요하지 리뷰어에게는 중요하지 않은 정보로 판단됐다.



위와 같은 이유로 다음과 같이 PR 템플릿을 수정했다.

```markdown
반드시 Check List를 모두 체크한 후에 PR을 요청

# Check List
- [ ] PR 제목에 티켓 ID를 넣었는가? (ex. OI-TKT-1430)
- [ ] PR 제목에 feat이나 fix, chore를 올바르게 넣었는가? (ex. \[OI-TKT-1430\] bug: 버그 수정)
- [ ] 테스트 코드를 작성하거나 배포 후 테스트를 해봤는가?
- [ ] Requirements 와 Description 을 작성했는가?

# Requirements
- 리뷰어가 작업에 대한 요구사항을 알 수 있도록 간단하게 설명

# Description
- 리뷰어가 코드를 이해하기 쉽도록 간단하게 작업 내용에 대해 설명
```

이처럼 PR을 올릴 때 확인할 항목들을 체크하고 리뷰어에게 필요한 정보만 작성할 수 있도록 수정했다.



#### 신기능 프로세스 설계

이번에 개발하게 된 비교탐지 기능에 대해 서빙 엔지니어와 같이 프로세스 설계를 진행했다.

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

위와 같이 1차로 프로세스 설계를 마쳤으며 세부적인 내용은 추가 설계가 필요하다.



#### CI/CD 버그 해결

사내 서버에 배포하여 사용중인 self-hosted Runner가 다음과 같이 Docker에 문제가 존재하여 CI/CD가 실패하는 문제가 발생하고 있다.

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Runner가 처음에는 정상 동작하지만 어느순간 부터 Docker가 동작하지 않는 것으로 확인된다. 백엔드의 모든 서버가 테스트를 수행할 때 Testcontainer를 사용하는데 이로 인한 문제인지 확인이 필요하다. 먼저 실패가 자주 일어나는 gradle build 부분을 self-hosted가 아닌 github-hosted runner를 사용하도록 수정하고 docker build하고 push하는 것만 self-hosted로 수정하여 테스트를 해봤다.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

결과를 확인해보니 gradle build는 성공했으나 docker build를 수행할 때 에러가 난 것으로 확인됐다. build-test-package Job에서 version 정보를 가져와서 tag에 넣어야 하지만 이 과정에서 문제가 발생한 것으로 추측되어 디버깅을 진행한 후에 수정이 필요할 것 같다.



### 개인 공부

#### Spring Events 학습

devooks(전자책 판매 플랫폼)라는 개인 프로젝트에 알림 기능을 개발하기 위해 어떤 방법으로 구현하는 것이 좋을지 조사를 진행했다.

처음에는 메시지 큐를 사용하여 알림 생성이 필요한 로직에서 이벤트를 발행하고 알림 도메인 측에서 해당 이벤트를 구독하여 알림을 저장하는 프로세스를 생각했다.&#x20;

어떤 메시지 큐를 사용할지 조사하던 중에 Spring AOP를 활용하여 도메인 로직에   이벤트 발행 로직을 넣지 않고 외부에 둘 수 있는 방법이 있는 것을 알게 되었다. 그래서 따로 메시지 큐를 사용하지 않고 Spring 내부에서 이벤트를 주고 받을 수 있도록 구현한 후에 AOP를 활용하여 결합도를 낮추고, 추후에 서비스가 커졌을 때 메시지 큐를 도입해도 괜찮을 것 같다는 결론을 내렸다.

위와 같은 이유로 Spring 자체 이벤트 발행 및 구독 방식을 구현해보기 위해 참고할만한 글을 찾던 중 다음 글을 발견하여 이를 보고 튜토리얼 프로젝트를 진행해 볼 예정이다.

{% embed url="https://www.baeldung.com/spring-events" %}



## 개선 및 목표

* gitflow를 빠르게 수정하여 CI/CD가 정상적으로 수행될 수 있도록 작업 필요
* Spring Events와 AOP를 활용하여 알림을 편리하게 저장할 수 있는 구조로 튜토리얼 프로젝트를 만들어서 사이드 프로젝트에 적용 필요

