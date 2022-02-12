---
description: 일일 회고 9회차
cover: .gitbook/assets/Frame 77.png
coverY: 233.12124582869856
---

# 🥱 2022.02.11

## 할일 및 한일

* [x] `배터리 관제 시스템` 백엔드 아키텍처 설계
* [ ] `배터리 관제 시스템` 의 사이트 관리 페이지 구현
* [ ] `Event Sourcing과 CQRS를 적용한 폴리글랏 구조 설계` 논문 정리

## 경험과 배움

### `배터리 관제 시스템` 백엔드 아키텍처 설계

현재 배터리 관제 시스템의 백엔드 기술 스택으로  `Spring WebFlux` 와 `JooQ` 를 사용하고 있다.

{% hint style="info" %}
_**WebFlux? JooQ?**_\
\
\- `WebFlux` : non-blocking과 reactive stream을 지원하는 reactive-stack web framework이다.\
&#x20; \- 논블로킹과 함수형 프로그래밍을 지원한다.\
\
\- `JooQ(Java Object Oriented Query)` : 데이터베이스에 저장된 테이블을 기반으로 \
&#x20;   Java 코드를 생성하고, 코드를 통해 Type Safe한 SQL 쿼리를 작성할 수 있도록 하는 라이브러리이다.\
\
[https://docs.spring.io/spring-framework/docs/5.2.6.RELEASE/spring-framework-reference/web-reactive.html#webflux-new-framework](https://docs.spring.io/spring-framework/docs/5.2.6.RELEASE/spring-framework-reference/web-reactive.html#webflux-new-framework)

[https://www.jooq.org/](https://www.jooq.org)
{% endhint %}

위와 같은 **비동기 프레임워크**와 **쿼리 지향 라이브러리**를 사용하는 이유는 `배터리 관제 시스템` 의 요구사항을 해결하기 위함이며, 요구사항은 다음과 같다.

1. 약 30,000 대의 배터리로부터 초당 한 건의 데이터를 수신해야한다. => **30,000 TPS**
2. 배터리로부터 수신된 데이터를 기반으로 통계 및 시각화를 해야한다. => **복잡한 쿼리 필요**

이처럼 대용량 트래픽을 수용하고 복잡한 쿼리를 작성하기 위한 백엔드 기술 스택을 선정하게 되었다.



이러한 기술 스택으로 백엔드를 개발하던 도중 WebFlux와 JooQ를 같이 사용하면 **트랜잭션 처리가 되지 않는 것**을 발견하여, 어떻게 진행할지 회의를 진행했다. 회의한 후 **'트랜잭션이 중요한 도메인과 그렇지 않은 도메인을 분리하자'** 라는 결과가 도출되어 백엔드 팀원 각자 아키텍처를 설계해 오기로 했다.



그래서 나는 아키텍처에 대해 고민하던 도중 배달의 민족과 같은 회사는 어떤 아키텍처를 채택해서 운영하고 있는지 궁금증이 생겨, 유튜브에 올라와 있는 **'배달의 민족 마이크로서비스 여행기'** 라는 영상을 보고 정리하게 되었다.

{% embed url="https://www.youtube.com/watch?ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech&v=BnS6343GTkY" %}
\[우아콘2020] 배달의민족 마이크서비스 여행기
{% endembed %}

{% embed url="https://equinox-dirigible-aec.notion.site/b500f2c0e9334f17893cd4c8d0c8386a" %}
유튜브 영상을 정리한 Notion 페이지
{% endembed %}



위의 영상을 통해 마이크로서비스와 CQRS를 도입하는 일련의 과정에 대해 배울 수 있었고, 굳이 대규모 시스템이 아니라면 마이크로서비스를 적용하는 것이 오버 엔지니어링이라는 것에 대해 알 수 있었다.



## 개선 및 목표

``
