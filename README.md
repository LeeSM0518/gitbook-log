---
description: 일일 회고 11회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.05

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

* [ ] 회사 업무
  * [ ] 백엔드 Github Actions 개선
  * [ ] 인퍼런스 결과 저장 실패 해결

## 경험 및 배움

### 회사 업무

#### 백엔드 Github Actions 개선

모든 저장소의 Github Actions를 개선을 완료하고 자동 배포 Action을 실행해봤을 때 모두 정상적으로 완료되는 것을 확인했다.

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Github Actions를 개선하여 안정화를 완료했지만 다음과 같이 개선할 것은 많이 보인다.

1. 캐시를 활용하여 빌드 속도 개선
2. self-hosted Runner의 문제를 해결한 후 Github 자체 Runner를 사용하지 않도록 수정
3. 자동 배포가 전부 완료됐을 때 released 라벨을 남기도록 수정
4. 이미지를 배포할 경우 배포된 커밋에 tag 남기도록 수정
5. 이미지를 배포할 커밋에 tag가 남아 있을 경우 배포를 수행하지 않도록 수정
6. 배포시 Release Draft 남기도록 수정

위 항목들은 개선이 되면 좋지만 필수적인 것은 아니므로 틈틈이 시간날 때 마다 개선하도록 하자.

## 앞으로 할 일

* [ ] 회사 업무
  * [x] 백엔드 Github Actions 개선
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
  * [ ] Webflux와 R2DBC에서의 페이징 활용법 조사
* [ ] 사이드 프로젝트
  * [ ] 알림 기능 설계
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
