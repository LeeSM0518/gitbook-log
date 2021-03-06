---
description: 일일 회고 30회차
cover: .gitbook/assets/Frame 85.png
coverY: 199.1713014460512
---

# 😔 2022.03.04

## 할일 및 한일

* [x] API Gateway 조사 및 정리
* [x] 배터리 관제 시스템 프로젝트 주간 미팅

## 경험 및 배움

### API Gateway 조사 및 정리

논문 세미나 발표 준비를 위해 기존에 진행하던 API Gateway를 조사한 내용을 기반으로 재정리를 진행했다.

{% embed url="https://equinox-dirigible-aec.notion.site/API-Gateway-e81410a14fa448bdb789edb163c2447e" %}
API Gateway 조사 및 정리 페이지
{% endembed %}



정리를 완료하고 Spring Cloud Gateway에서 캐시 기능을 제공한다는 것을 얼핏 봤었는데, 해당 기능이 어떻게 제공되는지 조사를 진행했다. 조사해본 결과, Spring Cloud Gateway에서는 특정 상황에서 요청 Body만 캐싱하는 것을 알 수 있었다. 아직 추가적인 조사가 필요하지만 `'마이크로서비스 최적화를 위한 API Gateway의 응답 캐시 자동화'` 라는 논문 주제가 나쁘지 않은 것을 느끼게 되었다.



### 배터리 관제 시스템 프로젝트 주간 미팅

프로젝트를 의뢰한 회사 측에서 차년도 요구사항을 우리 연구팀으로 전달하여, 해당 요구사항과 현재 프로젝트가 얼마나 진행되었는지 보고하는 주간 미팅을 진행했다.



## 개선 및 목표

* Spring Cloud Gateway 문서 정리 완료하기
* 현재 구현 중인 사이트 관리 페이지를 완료하고 새로운 요구사항에 대해 프론트 팀원과 계획세우기
