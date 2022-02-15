---
description: 일일 회고 12일차
cover: .gitbook/assets/Frame 77.png
coverY: 172.30700778642938
---

# 😌 2022.02.14

## 할일 및 한일

* [x] `배터리 관제 시스템` 의 사이트 관리 페이지 구현 (40%)
* [x] `ElasticSearch` 세미나

## 경험과 배움

### `배터리 관제 시스템` 의 사이트 관리 페이지 구현

오늘은 사이트 관리 페이지의 사이트 등록 기능 구현을 진행했다. 사이트를 등록할 때 주소와 상세주소, 우편번호, 위도, 경도 등이 필요하기 때문에 주소 API를 찾아보게 되었으며 찾은 API는 다음과 같다.

1. _**다음 주소 API**_
   1. Key 발급 필요 X
   2. 사용량 제한 X
   3. 위도, 경도 정보 X
2. _**행정안전부의 좌표제공 API**_
   1. Key 발급 필요 O
   2. 사용량 제한 X
   3. 위도, 경도 정보 O

원래는 다음 주소 API를 사용하려 했으나, 다음 주소 API는 위도와 경도 정보를 제공하지 않아 행정안전부의 좌표제공 API도 고려하게 되었다. 이 와중에 다음 주소 API가 Kakao 주소 API를 연동하면 주소에 대한 좌표를 가져올 수 있는 것을 알게 되었다.&#x20;

현재 배터리 관제 시스템에서 카카오 로그인을 사용하므로 행정안전부의 좌표제공 API를 사용하게 되면 카카오 인증키와 좌표제공 인증키 두 개를 관리해야 하기 때문에, 관리 포인트를 줄이기 위해 다음 주소 API와 Kakao 주소 API를 연동해서 사용하기로 했다.



{% embed url="https://postcode.map.daum.net/guide" %}
다음 주소 API
{% endembed %}

{% embed url="https://www.juso.go.kr/addrlink/devAddrLinkRequestGuide.do?menu=coordApi" %}
행정안전부 좌표제공 API
{% endembed %}



### `ElasticSearch` 세미나

Redis 세미나가 끝나고 이어서 ElasticSearch 세미나를 진행하게 되었다.

아직은 ElasticSearch에 대한 이해도가 적기 때문에 간단하게 ElasticSearch가 무엇인지와 어떻게 사용하는지에 대해서만 정리해놨다.

{% embed url="https://equinox-dirigible-aec.notion.site/ElasticSearch-887239aae0604e5ab20ccd01ca5b62b8" %}
ElasticSearch 정리 페이지
{% endembed %}

## 개선 및 목표

* 다음 주소 API와 카카오 주소 API를 연동한 사이트 등록 기능 구현하기
* ElasticSearch가 무엇이고 어느 상황에서 사용하는지 조사하기

