---
description: 일일 회고 23회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.21

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
  * [x] 영상 입력 실패 해결
  * [x] 썸네일 생성 실패 해결

## 경험 및 배움

### 회사 업무

#### 영상 입력 실패 해결

* 영상과 관심지역을 intersection 하면 GeometryCollection 객체가 생성되어 에러 발생
  * intersection 로직이 Polygon이나 MultiPolygon 일 경우에만 처리하도록 되어 있음
  * GeometryCollection일 경우 내부 Geometry 목록을 조회한 후 각 Geometry의 area를 추출하여 해당 값을 모두 더하도록 수정
  * Polygon이나 MultiPolygon이 아닌 GeometryCollection 객체가 생성된 이유 파악 필요



#### 썸네일 생성 실패 해결

* 영상을 png 타입으로 썸네일을 조회할 경우 밴드별 색상 정보가 잘못 입력되어 있으면 에러가 발생하는 것을 확인 (ex. Gray, Undefined, Undefined, Undefined)
* 색상 정보를 Red, Green, Blue, Alpha로 변경했더니 UI에서 영상이 조회되지 않는 문제 발생
* 문제 해결 방법에 대해 논의하여 임시로 Red, Green, Blue, Undefined로 설정하기로 결정
* 이번주 내로 데모를 수행해야 하므로 빠르게 입력한 후에 원인 파악과 문제 해결을 하기 위함



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
