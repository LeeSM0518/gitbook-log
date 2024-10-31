---
description: 일일 회고 22회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.20

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
  * [x] 파드들이 가끔씩 다운되는 문제 해결
  * [x] 영상의 썸네일 생성 실패 해결

## 경험 및 배움

### 회사 업무

#### 파드들이 가끔씩 다운되는 문제 해결

* 파드들이 주기적으로 ContainerStatusUnknown 상태로 변하고 다운됨
* describe를 해보니 다음과 같은 메시지가 띄워져 있음
  * The node was low on resource: ephemeral-storage. Container ovision-server was using 3108Ki, which exceeds its request of 0.
* 인프라팀에 확인 요청 후 다음과 같이 pod의 자원을 변경 요청
  * resources
    * requests
      * ephemeral-storage : 500Mi
    * limits
      * ephemeral-storage : 1Gi
* deployment 파드들의 resource를 위와 같이 변경



#### 영상의 썸네일 생성 실패 해결

* 영상이 3밴드 영상이지만 메타데이터에는 Gray로 되어 있으므로 데이터 자체에 문제가 있는 것으로 판단
* 데이터 엔지니어에게 RGB로 넣는 작업을 요청
* RGB로 변경된 영상을 저장하니 red, green, blue, alpha 가 존재하여 프론트에서 영상 조회를 실패함
* 현재 상황과 해결 방안을 정리하여 팀원들과 논의하여 해결 방향을 결정



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log/daily-log-2024/2024.08.05#github-actions))
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용
  * [ ] 에러 로깅 적용
