---
description: 일일 회고 28회차
cover: .gitbook/assets/Frame 77.png
coverY: 177.34198331788693
---

# 🥱 2022.03.02

## 할일 및 한일

* [x] `API Gateway` 학습 및 정리
* [x] `배터리 관제 시스템` 의 사이트 관리 페이지 수정

## 경험 및 배움

### API Gateway 학습 및 정리

졸업 논문 주제로 API Gateway의 응답 캐시 자동화를 구상하고 있으므로, 먼저 API Gateway가 정확히 무엇이며 어떤 역할을 하는지 알기 위한 학습을 진행했다.

기존에 마이크로서비스를 학습하기 위해 구매했던 책인 `'마이크로서비스 패턴'` 에서 API Gateway 파트를 찾아서 정리를 진행했다.



{% embed url="http://www.yes24.com/Product/Goods/86542732" %}
마이크로서비스 패턴 도서
{% endembed %}



이 책에서는 API Gateway를 사용하지 않고 구축한 서비스에 대한 단점을 얘기하면서, API Gateway가 무엇이고 어떤 역할을 하는지 쓰여있었다. 또한 API Gateway의 아키텍처와 장단점, 설계 및 구현 이슈 대한 내용이 있었다.



{% embed url="https://equinox-dirigible-aec.notion.site/API-Gateway-6fc751fe8dc6430ba2f296fb8c9f8d69" %}
API Gateway 정리 페이지
{% endembed %}



해당 책을 보면서 정리를 하다보니 Spring Cloud Gateway가 비동기 스팩으로 구현되어 있는 것을 배웠으며, API Gateway에 대한 이해도를 높이려면 Spring Cloud Gateway 공식 도큐먼트를 살펴봐야 할거 같다는 생각이 들어 공식 도큐먼트 정리를 진행했다.



{% embed url="https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html" %}
Spring Cloud Gateway Docs
{% endembed %}



도큐먼트에는 Spring Cloud Gateway를 사용하는 방법과 용어, 동작 원리, route & filter 구성 방법 등이 쓰여있었다. 아직 많이 정리하지 못 해서 다음주나 다다음주까지 정리를 완료할 예정이며, Spring Cloud Gateway를 간단하게 사용해보는 예제 프로젝트를 만들어볼 예정이다.



{% embed url="https://equinox-dirigible-aec.notion.site/Spring-Cloud-Gateway-be9030935ab947baa61e9717dd82b8ba" %}
Spring Cloud Gateway 정리 페이지
{% endembed %}



### `배터리 관제 시스템`의 사이트 관리 페이지 수정

기존의 사이트 상세 조회 API의 응답 Body에 관리자와 위도, 경도 정보를 담지 않아 제대로 된 상세 조회를 구현하지 못 했었으나, 백엔드를 구현하는 팀원이 수정을 완료하고 배포하여 해당 부분을 수정하는 것을 진행했다. 이 작업은 API를 호출하는 부분과 데이터를 바인딩하는 부분만 변경하면 되므로 금방 해결한 것 같다.



![사이트 상세 조회 기능](<.gitbook/assets/Screen Shot 2022-03-04 at 4.14.11 PM.png>)



## 개선 및 목표

* Spring Cloud Gateway 문서 정리 완료하기
* 사이트 관리자를 변경하는 기능이 존재하지 않으므로 해당 기능 구현하기

