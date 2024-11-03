---
description: 일일 회고 40회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.11.02

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

UI에 변화 탐지 영역이 불규칙하게 보이는 문제가 발생하여 이를 해결하는 작업을 이어서 진행했다. 내가 원하는 결과는 줌 레벨마다 데이터가 상세하게 보이는 것이었지만 낮은 줌 레벨에서 보이던 정보가 높은 줌 레벨에서는 사라지는 문제로 확인됐다. 이는 쿼리에서 필터링할 때 문제가 발생하는 것으로 예측되어 POLYGON과 MULTIPOLYGON만 조회하는 쿼리만 따로 실행해서 결과를 확인했다.

```sql
SELECT
    polygon
FROM (
    SELECT st_makevalid((st_dump(st_snaptogrid(changed_area::geometry, :precision))).geom) AS polygon
    FROM change_detection_result
    WHERE scene_id = :sceneId
      AND project_id = :projectId
) sub
WHERE GeometryType(polygon) IN ('POLYGON', 'MULTIPOLYGON')
```

&#x20;쿼리를 실행해봤을 때는 문제가 없는 것처럼 보였으나 WHERE 절을 제거한 후에 확인해보니 어떤 문제가 있는지 명확하게 알 수 있었다. st\_makevalid 함수의 파라미터로 전달된 POLYGON 정보가 GEOMETRYCOLLECTION 정보로 변환되어 필요한 정보임에도 불구하고 필터링이 되어 버린 것이다.&#x20;

이를 해결하기 위해 st\_makevalid를 통해 POLYGON 정보가 유효하도록 변환하는 것이 아닌 st\_buffer 함수를 사용해서 POLYGON 정보를 유효하도록 변환하는 쿼리로 변경했다. 두 함수의 목적은 약간 다르지만 st\_buffer 함수의 파라미터에 buffer 값으로 0을 전달할 경우 지오메트리 정보를 유효하게 변경하는 목적으로 동작이 유사해진다. st\_buffer로 변경하고 쿼리를 실행해 보니 원하는 결과가 출력되는 것을 볼 수 있었다. st\_makevalid는 지오메트리 정보가 유효하지 않더라도 원본을 유지하면서 지오메트리를 유효하게 변환하여 GEOMETRYCOLLECTION으로 변환됐고, st\_buffer는 지오메트리 정보가 유효하지 않을 경우 해당 부분이 제거되어 POLYGON으로 변환되었다. 특수한 경우에서는 st\_buffer에서도 GEOMETRYCOLLECTION이 응답되거나 필요한 부분이 제거될 수도 있으므로 추후에는 더 안전한 방법으로 변경이 필요할 것 같다. 쿼리를 수정한 후에 테스트 서버에 배포하여 UI로 확인해보니 정상 동작하는 것으로 확인됐다.&#x20;



#### 영상 조회 실패 현상 해결

영상의 타일 정보를 한 번에 많이 조회하다 보니 가끔 요청이 Pending 상태로 멈춰 있는 현상이 발생했다. 영상 조회 서버의 성능 문제로 예상되어 서버의 파드 개수를 4개에서 8개로 늘려봤다. 조회되는 속도는 빨라졌으나 동일하게 Pending이 발생하여 12개로 늘린 후에 다시 확인해 봤다. 이 또한 동일하게 Pending이 발생하여 다른 문제인가 싶었지만, 또 어느 순간부터는 Pending이 발생하지 않게 되었다. 그 후에는 정확한 원인 파악을 하지 못하여 다음에 동일한 문제가 발생할 경우 다시 분석해 볼 예정이다.



#### 이벤트 소비 실패 해결

특정 서버에서 이벤트가 정상적으로 소비되지 않고 무한 실패하고 있는 것을 발견했다. 이를 해결하기 위해 어느 부분에서 소비를 실패하는지 분석했다. 문제가 발생한 이벤트는 특정 서버에서 변경된 정보를 다른 서버들로 동기화하는 이벤트였다. 해당 이벤트를 받아서 변경되는 정보를 조회한 후에 변경을 수행하게 되는데 ID로 정보를 조회했을 때 동일한 ID가 존재하지 않아 발생하는 문제였다. 이처럼 ID가 존재하지 않아서 예외가 발생하고 이벤트를 다시 메시지 큐에 넣어 무한 실패를 하게 된 것이었다.

이를 해결하기 위해 먼저 ID가 존재하지 않을 경우 ID가 존재하지 않는다는 예외를 발생시키도록 수정하고, 이벤트를 처리할 때 예외가 발생할 경우 해당 메시지는 제거하도록 수정했다. 수정한 코드를 문제가 발생하던 서버로 배포하니 버그가 해결되어 정상 동작을 수행했다.

이처럼 잘못된 이벤트가 발생했을 때 이벤트를 제거하게 되면 디버깅이나 데이터 복구가 어렵다. 추후에는 이벤트 처리를 실패했을 때 데이터베이스에 실패한 이벤트를 저장하거나 실패 이벤트를 다른 큐로 발행하여 따로 처리하도록 수정이 필요해 보인다.



### 사이드 프로젝트

#### 이메일 확인 API 구현

사용자가 이메일을 등록할 때 이메일을 잘 작성했는지 확인할 수 있도록 이메일 확인 API를 구현했다. Spring에 Mail 라이브러리가 잘 되어 있어서 간편하게 구현할 수 있었으며 서버를 실행해서 테스트 해보니 잘 작동하는 것을 볼 수 있었다.

{% embed url="https://github.com/LeeSM0518/devooks/pull/13" %}

직접 서버를 실행한 후에 테스트 해보기 위해 테스트 코드를 작성하지 않았지만 Mock을 사용해서 테스트 코드를 작성해 보는 것이 좋을 것 같다.



## 해야 할 일

* [x] 회사 업무
  * [x] 영상 조회 버그 해결
  * [x] 변화 탐지 영역 조회 버그 해결
* [ ] 사이드 프로젝트
  * [ ] 테스트 CI 성능 개선
  * [ ] INVALID\_EMAIL 예외 메시지 오타 수정
  * [ ] Flyway 적용
