---
description: 일일 회고 15회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.09

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
  * [x] 신기능 개발 공수 산정
  * [x] 영상 입력 실패 해결
* [x] 사이드 프로젝트
  * [x] 알림 생성 테스트 구현

## 경험 및 배움

### 회사 업무

#### 신기능 개발 공수 산정

변화탐지 모델을 탑재하는 신기능 개발에 대해 공수를 산정하는 작업을 진행했다. 공수를 산정하기 위해서는 작업할 항목을 나열하고 해당 항목들에 대해 공수를 산정해야 한다. 이를 위해 현재까지 완료된 디자인과 디자인 리뷰를 진행하면서 결정된 것들을 토대로 작업 항목을 정리했다.

작업 항목을 정리하면서 어떤 것들을 구현해야 하고 기존 로직에서 어떠한 것들이 변경되는지를 나열했다. 이러한 과정에서 기획과 디자인에서 빠뜨린 항목들을 알 수 있었고, 신기능을 개발하기 위해 어떤 것들을 설계하고 구현하고 수정해야 하는지 명확하게 인지가 되었다. 차주에 나머지 작업 항목들을 정리하고 공수 산정을 완료할 예정이다.



#### 영상 입력 실패 해결

고객사로부터 영상 입력이 실패하는 에러 로그를 전달받아서 확인해 보니, 두 개의 Geometry를 intersection 하는 로직이 연속 호출되어 에러가 발생하는 것으로 판단되었다. intersection 로직을 한 번만 호출하도록 변경한 후에 서버 이미지를 생성하고, 고객사에 출장간 팀원에게 전달한 후에 배포 및 테스트를 요청했다. 이에 대한 결과는 차주에 확인이 가능할 것으로 판단된다.

사내에서는 위와 같은 에러가 재현되지 않아서 문제를 해결하는 데 어려움이 있다. 그렇다 하더라도 intersection을 하면 데이터가 어떻게 변환이 되고, intersection이 된 후에 Geometry가 유효하지 않을 경우 buffer(0.0)을 수행하는데 이것이 어떻게 처리가 되는지를 좀 더 분석하여 문제를 명확하게 파악할 필요가 있다. 차주에 고객사에서 정상적으로 동작이 되는지 확인한 후에 추가 작업을 할 예정이다.



### 사이드 프로젝트

#### 알림 생성 테스트 구현

어제 구현한 알림 생성 기능을 테스트 코드를 작성하는 작업을 진행했다. 테스트 코드 작성에 앞서 R2DBC는 Spring JPA와 다르게 자동으로 DDL을 생성 및 실행해주지 않기 때문에 DDL 쿼리 작성을 진행했다.

DDL 쿼리를 작성하면서 UUID 키를 어떤 버전을 사용하여 기본 값을 생성해줄 지 고민했다. 일반적으로 uuid-ossp 확장을 추가하여 uuid\_generate\_v4 함수를 많이 사용하지만, v4보다 v7이 삽입 속도나 인덱스를 활용한 조회 속도가 빠르기 때문에 v4를 사용할지 v7을 사용할 지 고민했다. 확인해보니 postgres 도커 이미지에는 uuid v7 을 제공하지 않으므로 uuid v7을 적용하기 위한 추가 작업이 요구되었다. 현재 구현하고 있는 알림 서비스는 튜토리얼 서비스이므로 v7을 사용하지 않고 단순하게 v4를 적용했다. 추후에 사이드 프로젝트에 uuid v7을 적용해보도록 하자.

## 앞으로 할 일

* [ ] 회사 업무
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log#undefined-2))
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
  * [ ] Webflux와 R2DBC에서의 페이징 활용법 조사
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용