---
description: 일일 회고 55회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 😟 2022.04.07

## 할일 및 한일

* [x] 논문 세미나 준비
  * [x] 논문 주제의 아키텍처 그리기

## 경험 및 배움

### 논문 세미나 준비

이번 논문 세미나때 내 논문 주제에 대해 발표하고 피드백을 받기 위해, 내 논문 주제의 시스템 아키텍처를 설계하는 것을 진행했다.



졸업 논문 주제는 `'IoT 환경에서의 마이크로서비스 최적화를 위한 API Gateway의 응답 캐싱 자동화'` 이며, 설계한 시스템 아키텍처는 다음과 같다.

![시스템 아키텍처](<.gitbook/assets/image (8).png>)



해당 아키텍처를 간단하게 설명하면 다음과 같다.

* Application이 실행되면 Middleware로 자신의 IP와 Port를 전송하여 서비스 등록이 수행된다.
* Command Application은 쓰기 요청만을 담당하며, Query Application은 읽기 요청만을 담당한다.
* Gateway는 Client의 HTTP 요청 정보를 확인하여 응답 캐싱을 요구하는지를 검토하고, 만약 캐싱을 요구하는 요청이라면 In-Memory DB로부터 데이터를 조회한다.
* 만약 Client가 요청 캐싱을 요구하면, In-Memory DB에 데이터를 저장한 후 Data Synchronizer가 RDBMS에 동기화를 진행한다.
* Command Application은 Master RDBMS에 데이터를 저장하고, 저장된 데이터는 Slave RDBMS로 복제된다.



이로써 Client의 요청과 응답을 캐싱하여 성능이 향상되는 것과 Gateway에서 캐싱을 담당하여 Application 측에서는 캐싱에 대해 관여를 할 필요가 없게 되어 복잡도가 낮아지는 것을 기대할 수 있다.



하지만 현재 Spring Cloud Eureka와 RedisGears에 대한 지식이 부족하므로 각 기술의 공식 도큐먼트를 찾아서 정리해볼 예정이다.

## 개선 및 목표

* Spring Cloud Eureka와 RedisGears의 공식 도큐먼트 정리하기
