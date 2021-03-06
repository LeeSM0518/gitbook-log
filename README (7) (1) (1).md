---
description: 일일 회고 26일차
cover: .gitbook/assets/Frame 85.png
coverY: 208
---

# 🙂 2022.02.28

## 할일 및 한일

* [x] Elasticsearch 세미나 청강
* [x] API Gateway 조사

## 경험 및 배움

### Elasticsearch 세미나 청강

오늘은 Elasticsearch 인스턴스로 클러스터를 구축하고 운영하는 방법에 대해 알아보고 실습을 진행했다.&#x20;

이번 세미나를 진행하면서 Elasticsearch가 Java로 구현된 것을 알게 되었으며, `jvm.options` 설정 파일에서 JVM 힙 영역의 크기를 결정할 수 있고, GC를 변경할 수 있음을 배웠다.&#x20;

Elasticsearch에서 예전에는 CMS라는 GC 방식을 사용했지만, 최근 버전에서는 G1GC가 사용되는 것을 배웠으며 추후에 GC에 대해서도 학습할 예정이다.

{% embed url="https://equinox-dirigible-aec.notion.site/ElasticSearch-887239aae0604e5ab20ccd01ca5b62b8" %}
Elasticsearch 세미나 정리 페이지
{% endembed %}



### API Gateway 조사

석사 졸업 논문으로 API Gateway에 응답 캐시 적용을 자동화하는 아이디어를 구상했으나, 논문 세미나때 API Gateway에 대한 지식 습득과 Spring Cloud Gateway의 캐시 관련 조사가 부족하다는 피드백을 받았다.

먼저 API Gateway 관련 지식을 습득하기 위해 마이크로서비스 패턴 책을 참고하기로 했다.

{% embed url="http://www.yes24.com/Product/Goods/86542732" %}
참고 도서
{% endembed %}

{% embed url="https://equinox-dirigible-aec.notion.site/API-Gateway-6fc751fe8dc6430ba2f296fb8c9f8d69" %}
API Gateway 정리 페이지
{% endembed %}

위의 책을 참고하여 정리하면서 API Gateway의 기능과 장단점, 설계 및 구현 이슈에 대해 알게 되었다. API Gateway 지식 습득을 마치면 간단하게 API Gateway를 구현해 볼 예정이다.

## 개선 및 목표

* GC 조사 및 학습하기
* API Gateway 정리하기
* Spring Cloud Gateway를 활용하여 API Gateway 구현하기
