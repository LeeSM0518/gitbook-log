---
description: 일일 회고 38회차
cover: .gitbook/assets/Frame 85.png
coverY: 199.56626506024097
---

# 😙 2022.03.13

## 할일 및 한일

* [x] ElasticSearch 세미나 발표 준비

## 경험 및 배움

### ElasticSearch 세미나 발표 준비

오늘은 어제 다하지 못한 ElasticSearch 세미나 발표 준비를 Notion에 정리를 하고 해당 정리 내용을 기반으로 PPT를 만드는 식으로 진행했다.



첫 번째로 색인 성능 최적화 챕터 정리를 진행했으며 다음 Notion 페이지에 정리를 완료했다.

{% embed url="https://equinox-dirigible-aec.notion.site/10-494adcca04814b1083800a7ee751cfe3" %}
색인 성능 최적화 정리 페이지
{% endembed %}



색인 성능 최적화 챕터에서는 매핑 정보를 등록하는 방식에 따른 성능 차이를 알 수 있었으며, 여러 예제를 수행해보다가 `time` 이라는 셸 명령어를 알게 되었다.

{% hint style="info" %}
`time` : 요청한 명령의 실행 시간을 측정해주는 셸 명령어
{% endhint %}



그리고 ElasticSearch가 문서를 색인하는 작업과 이 작업의 주기를 설정하여 성능을 향상시킬 수 있음을 알게 되었다.   그 다음으로 대량의 문서를 한 번에 모아서 처리하는 API에 대해 알 수 있었으며, 이 API를 호출할 때 사용되는 Content-Type 중에 `ndjson` 이라는 것이 있는 것을 알게 되었다.

{% hint style="info" %}
`ndjson` : Newline Delimited JSON의 축약어로써 줄바꿈으로 구분된 JSON을 나타낸다. \


\
_Reference_\
[http://ndjson.org/](http://ndjson.org)
{% endhint %}





이렇게 색인 성능 최적화에 대한 내용을 정리를 마치고, 그 다음의 챕터인 검색 성능 최적화의 내용 정리를 진행했다.

{% embed url="https://equinox-dirigible-aec.notion.site/11-fd88f6b1d06e4df3bc50e0f59508bcc5" %}
검색 성능 최적화 정리 페이지
{% endembed %}



이 챕터에서는 ElasticSearch의 캐시의 종류들과 특성과 검색 쿼리 튜닝, 샤드 배치, forcemerge API 등에 대해 알 수 있었다.&#x20;

검색을 할 때 단순 패턴 매칭인지 집계인지, 정렬인지에 따라 캐시 영역을 따로 구성하는 것에 대해 배울 수 있었으며, `copy_to` 와 같은 검색 성능을 향상시키는 기능이 존재하는 것을 배웠다.

또한 클러스터를 구성할 때 샤드 개수에 따라 성능이 줄어들거나 향상될 수 있음을 알 수 있었고, 세그먼트를 병합하여 디스크 I/O를 적게 발생시켜 성능을 향상시킬 수 있음을 배웠다.



마지막으로 ElasticSearch 클러스터 구축 시나리오 정리를 진행했는데 해당 챕터는 아직 이해가 정확히 되지않은 상태이므로 다시 정리해서 발표 준비를 할 예정이다.



{% file src=".gitbook/assets/ElasticSearch 10, 11.pdf" %}
색인 성능 최적화와 검색 성능 최적화 발표 자료
{% endfile %}

## 개선 및 목표

* ElasticSearch 클러스터 구축 시나리오 재정리 후 발표 준비하기

