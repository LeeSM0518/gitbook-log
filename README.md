---
description: 일일 회고 30회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.21

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
  * [x] 대량 변화 탐지시 조회 성능 개선
* [x] 사이드 프로젝트
  * [x] Repository 마이그레이션

## 경험 및 배움

### 회사 업무

#### 대량 변화 탐지시 조회 성능 개선

이전에 검증했던 쿼리를 기반으로 변화 탐지 영역을 조회하는 API를 구현하는 것을 진행했다. 테스트 코드를 구현하고 정상 동작하는 것을 확인하여 테스트 서버에 배포한 후에 프론트에서 정상 동작하는지 확인하고 성능을 측정해 볼 예정이다.



### 사이드 프로젝트

#### Repository 마이그레이션

기존의 private 접근 제한으로 생성했던 Repository를 public 접근 제한의 Repository로 마이그레이션 하는 작업을 진행했다. private 접근 제한으로 되어 있을 경우 CI/CD가 유료이기 때문에 서비스를 출시하기 전 까지는 public 접근 제한으로 무료 CI/CD를 사용하기 위함이다.

{% embed url="https://github.com/LeeSM0518/devooks" %}
마이그레이션 한 Repository
{% endembed %}

AWS EC2를 활용해 서버를 새롭게 구성했으며 해당 서버로 자동 배포하도록 CI/CD를 구축할 예정이다. Github Issue와 Commit, Branch, PR 등을 Jira에서 볼 수 있도록 하기 위해 먼저 Github과 Jira를 연동하는 작업을 할 예정이다. 그 후 CI/CD가 특정 브랜치로 병합되었을 때 수행될 수 있도록 Gitflow 정책을 정리할 예정이다.



## 앞으로 할 일

* [ ] 회사 업무
  * [x] 대량 변화 탐지시 조회 성능 개선
  * [ ] 변화 탐지 영역 조회 API 성능 측정
* [ ] 사이드 프로젝트
  * [ ] Github & Jira 연동
  * [ ] Gitflow 정책 정리
  * [ ] CI/CD 구축
