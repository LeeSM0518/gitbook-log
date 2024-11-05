---
description: 일일 회고 36회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.28

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

## 경험 및 배움

### 회사 업무

#### 변화 탐지 영역 조회 API 버그 수정

MultiPolygon 값을 geometry와 geography 각 타입으로 변환한 값에 st\_area 함수를 사용해서 면적을 계산한 값을 확인해보니 값 차이가 매우 많이 났다.

```sql
SELECT st_area('MULTIPOLYGON(((127.894084 38.857241,127.894084 38.875008,127.91865 38.875008,127.91865 38.857241,127.894084 38.857241)))'::geometry)  AS geometry,
       st_area('MULTIPOLYGON(((127.894084 38.857241,127.894084 38.875008,127.91865 38.875008,127.91865 38.857241,127.894084 38.857241)))'::geography) AS geography;
```

| geometry               | geography         |
| ---------------------- | ----------------- |
| 0.00043646412199985565 | 4205221.103330612 |



geometry는 평면 좌표계를 사용해서 면적을 계산하고 결과는 degree^2으로 나오고, geography는 구형 좌표계를 사용하며 결과는 m^2으로 나오는 것을 확인했다. 그러므로 차이가 클 수 밖에 없으며 geography로 면적을 계산하는 것이 훨씬 정확하다는 것을 알 수 있다. 이와 같은 이유로 st\_area 함수를 사용하는 코드들에서 모두 geography로 타입을 변환한 후에 st\_area 함수를 호출하도록 수정했다. 그 다음으로 테스트 코드를 수행하여 정상 동작하는 것을 확인하고, 빌드한 후에 테스트 서버에 배포하고 API 테스트를 하여 정상 동작하는 것을 확인했다.

이전에는 geometry와 geography의 타입을 명확하게 이해하지 못 한채 사용했던 것 같다. 이후로 geometry와 geography로 값을 변환하거나 관련 함수를 사용할 때, 각 타입이 어떤 타입이며 어떻게 사용하는 것이 좋을지를 고민하면서 활용하자.



## 해야 할 일

* [ ] 회사 업무
  * [ ] 변화 탐지 영역 조회 API 성능 측정
  * [ ] GDAL 테스트 환경 개선
* [ ] 사이드 프로젝트
  * [ ] CI/CD 구축
  * [ ] 테스트 CI 성능 개선