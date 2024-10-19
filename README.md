---
description: 일일 회고 28회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.10.18

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

* [x] 회사 업무
  * [x] 대량 변화 탐지시 조회 성능 측정

## 경험 및 배움

### 회사 업무

#### 대량 변화 탐지시 조회 성능 측정

**GIST 공간 인덱스를 생성**한 후에 st\_intersection을 수행해 본 결과로 인덱스 생성 전과 동일하게 2분이 소요되는 것을 확인했다. 인덱스는 검색의 효율성을 높이는 것이므로 공간 연산을 하는데 영향이 없을 것이라고 예상한 것과 동일하게 **st\_intersection의 성능이 향상되지 않았다.**

공간 인덱스 검증을 마치고 **st\_snaptogrid 방식의 해결 방법 검증**을 진행했다. st\_snaptogrid는 파라미터로 precision 이라는 정밀도 값이 입력되는데 해당 값을 적절하게 넣어주기 위한 방법을 고민하여, 지도에서 줌 레벨별 픽셀 길이를 위도로 환산한 값을 넣는 것을 생각해냈다. 또한 **높은 줌 레벨**에서는 많은 결과가 존재할 수 있으므로 사용자가 보이는 부분에 대해서만 조회할 수 있도록 **보이는 영역에 대해서만 st\_intersects 함수를 수행**하도록 쿼리를 작성했다.

```sql
WITH snap_polygons AS
         (SELECT ((st_dump(st_snaptogrid(changed_area::geometry, :precision))).geom) polygon
          FROM change_detection_result
          WHERE scene_id = :sceneId
            AND project_id = :projectId)
SELECT st_multi(st_union(polygon))
FROM snap_polygons
WHERE st_intersects(polygon, :viewport);
```

기존에는 약 2분 걸리던 쿼리가 개선한 쿼리는 약 **500ms로 개선**된 것을 확인했다. 이 방법을 활용하여 API를 개선하는 작업을 진행할 예정이다. 이로써 조회 성능을 개선시키고 프론트에서 대량의 폴리곤을 효율적으로 보여줄 수 있을 것으로 판단된다.



## 앞으로 할 일

* [ ] 회사 업무
  * [x] 대량 변화 탐지시 조회 성능 측정
    * [x] st\_snaptogrid 방식의 해결 방법 검증
    * [x] 공간 인덱스 검증
  * [ ] 대량 변화 탐지시 조회 성능 개선
* [ ] 사이드 프로젝트
  * [ ] 알림 기능 테스트 구현
