---
description: 일일 회고 38회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.30

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

#### 변화 탐지 영역 조회 버그 해결

변화 탐지 영역 조회 API를 디버깅 해보니 변화 탐지 영역 조회시 탐지되지 않았으면 `GEOMETRYCOLLECTION EMPTY` 값으로 조회되어 MULTIPOLYGON 으로 변환하는 로직에서 에러가 발생했다. 이를 해결하기 위해 값이 존재하지 않을 경우 반드시 MULTIPOLYGON EMPTY로 반환될 수 있도록 쿼리를 다음과 같이 수정했다.&#x20;

```sql
SELECT st_astext(COALESCE(
        st_multi(st_intersection(st_envelope(changed_area::geometry), st_setsrid(
                'POLYGON ((-0.0543915950335041 -0.0364031555636757, -0.0543915950335041 0.0370679087372849, 0.0566733158551677 0.0370679087372849, 0.0566733158551677 -0.0364031555636757, -0.0543915950335041 -0.0364031555636757))'::geometry,
                4326))),
        ST_Multi(ST_GeomFromText('MULTIPOLYGON EMPTY'))
                 )) AS fallback_area
FROM change_detection_result
WHERE scene_id = 'wv3_20241015000000_1'
  AND project_id = '236f52ed-e7b2-4691-bea3-c3a6f3283336'
  AND st_intersects(changed_area,
                    'POLYGON ((-0.0543915950335041 -0.0364031555636757, -0.0543915950335041 0.0370679087372849, 0.0566733158551677 0.0370679087372849, 0.0566733158551677 -0.0364031555636757, -0.0543915950335041 -0.0364031555636757))'::geometry)
UNION ALL
SELECT st_astext(ST_Multi(ST_GeomFromText('MULTIPOLYGON EMPTY')))
WHERE NOT EXISTS (SELECT 1
                  FROM change_detection_result
                  WHERE scene_id = 'wv3_20241015000000_1'
                    AND project_id = '236f52ed-e7b2-4691-bea3-c3a6f3283336'
                    AND st_intersects(changed_area,
                                      'POLYGON ((-0.0543915950335041 -0.0364031555636757, -0.0543915950335041 0.0370679087372849, 0.0566733158551677 0.0370679087372849, 0.0566733158551677 -0.0364031555636757, -0.0543915950335041 -0.0364031555636757))'::geometry))
LIMIT 1;
```

수정한 후 테스트 서버에 배포하여 에러가 발생했던 요청으로 API를 호출해보니 정상적으로 응답되는 것을 볼 수 있었다.

하지만 그 뒤로 프론트에서 새롭게 배포한 버전으로 테스트 해봤을 때 동일한 에러가 발생하는 것을 확인했다. 해당 에러가 왜 발생했는지는 자세하게 봐야되겠지만 쿼리를 통해 NULL 처리를 하는 것이 아니라 코드쪽에서 NULL 처리를 하는 것이 더 안전할 것 같다. 추가로 테스트 코드를 여러 케이스를 고려해서 작성할 예정이다.



## 해야 할 일

* [ ] 회사 업무
  * [ ] 영상 조회 버그 해결
  * [ ] 변화 탐지 영역 조회 버그 해결
  * [ ] 변화 탐지 영역 조회 API 성능 측정
  * [ ] GDAL 테스트 환경 개선
* [ ] 사이드 프로젝트
  * [ ] 테스트 CI 성능 개선
