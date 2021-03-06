---
description: 일일 회고 51회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 😆 2022.04.01

## 할일 및 한일

* [x] 논문 세미나 발표 준비

## 경험 및 배움

### 논문 세미나 발표 준비

논문 세미나 발표 준비를 하기 위해 내 논문 주제와 유사한 것을 제공하는 서비스들을 조사해보고 분석하는 것을 진행했다. API Gateway를 통해 응답 캐싱 기능을 제공하는 서비스는 Amazon과 Oracle, Google Cloud Platform이 있었다. 아직은 많이 조사하지 못 하여, 더 많이 조사하고 정리한 뒤 새롭게 알게된 것에 대한 느낀점을 작성할 예정이다.

이처럼 내가 작성할 졸업 논문의 주제와 유사한 서비스들이 이미 제공되고 있음을 알게 되어, Redis의 Write-Behind 기능을 적용하는 것도 현재 고려중이다. 이는 사용자로부터 전송 데이터를 먼저 레디스에 저장한 후, 해당 데이터를 비동기식으로 데이터 저장소에 반영하는 방식이다. 이 방식을 사용하면 사용자의 POST 요청에 대해 빠르게 응답을 할 수 있게 되는 장점이 있다.

## 개선 및 목표

* API Gateway Cache 기능을 제공하는 서비스들에 대해서 조사하기
* Redis Write-Behind에 대해서 조사하기
