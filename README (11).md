---
description: 일일 회고 17회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.11

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

* [x] 개인 학습
  * [x] WebFlux와 R2DBC를 활용한 페이징 사용 조사
* [x] 사이드 프로젝트
  * [x] 알림 페이징 조회 구현

## 경험 및 배움

### 개인 학습

#### WebFlux와 R2DBC를 활용한 페이징 사용 조사

[_"Pagination in Spring Webflux and Spring Data Reactive"_](https://www.baeldung.com/spring-data-webflux-pagination) 페이지에 Spring Webflux와 Spring Data Reactive를 사용하여 Pagination을 구현하는 방법이 작성되어 있어서 해당 글을 정리하는 것을 진행했다. 기본 개념은 정리를 했으나 예제 코드는 개인 프로젝트에 적용해보고 정리해서 글을 작성할 예정이다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-tech/spring/pagination" %}

### 사이드 프로젝트

#### 알림 페이징 조회 구현

위에서 언급한 페이지에는 구현 예제가 자바로 되어 있으며 Mono와 Flux를 사용하여 적용되어 있었다. 나는 이 내용을 코틀린과 코루틴으로 변경하여 예제를 구현했다.&#x20;

{% embed url="https://github.com/LeeSM0518/notification-service/commit/a1bfabd573a3fb89b5c293eb4cc969400101317b" %}
예제 코드
{% endembed %}

이 과정에서 Spring에서 제공하는 Page 객체를 그대로 응답 객체로 반환하도록 구현했더니, Page 객체는 기본 생성자를 제공하지 않아서 Jackson 라이브러리에서 에러가 발생했다. 그래서 Page 정보를 반환할 때 공통적으로 쓸 PageResponse 클래스를 정의하였으며, 해당 클래스를 반환하도록 변경한 후 테스트를 수행해보니 정상적으로 동작하는 것을 볼 수 있었다.&#x20;



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log#undefined-2))
  * [ ] 주기적으로 실행하는 로직에 Timer 적용
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
  * [ ] Webflux와 R2DBC에서의 페이징 활용법 조사
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용
  * [ ] 에러 로깅 적용
