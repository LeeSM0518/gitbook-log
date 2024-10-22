---
description: 일일 회고 31회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.22

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
  * [x] 변화 탐지 영역 조회 API 버그 수정
* [x] 사이드 프로젝트
  * [x] Github과 Jira 연동
  * [x] Gitflow 정책 정리
  * [x] CI/CD 구축

## 경험 및 배움

### 회사 업무

#### 변화 탐지 영역 조회 API 버그 수정

이전에 조회 성능을 개선하여 구현한 변화 탐지 영역 조회 API를 테스트 서버에 배포하고 테스트를 수행했다. 처음에는 정상 동작 하는줄 알았으나 잘못된 데이터가 나왔으며 에러도 발생했다. 그 이유는 데이터가 존재하지 않을 경우 NULL로 반환되는데 해당 데이터를 Not Null로 처리하여 에러가 발생했으며, st\_snaptogrid 함수를 통해 변환된 데이터가 유효하지 않도록 생성되어 에러가 발생했다.

먼저 값이 NULL로 반환되지 않도록 COALESCE 함수를 사용하여 NULL일 경우 기본값을 제공하도록 구성했다. 추가로 NULL이 아니라 값이 아예 존재하지 않아서 NULL 조차 나오지 않는 경우에 대해서는 UNION ALL을 사용해 기본 값을 제공하도록 쿼리를 구성했다. 해당 쿼리는 다음과 같다.

```sql
SELECT COALESCE(
           ST_Multi(ST_Intersection(st_envelope(changed_area::geometry),:viewport)),
           ST_Multi(ST_GeomFromText('MULTIPOLYGON EMPTY'))
       ) AS fallback_area
FROM change_detection_result
WHERE scene_id = :sceneId
  AND project_id = :projectId
UNION ALL
SELECT ST_Multi(ST_GeomFromText('MULTIPOLYGON EMPTY'))
WHERE NOT EXISTS (
    SELECT 1
    FROM change_detection_result
    WHERE scene_id = :sceneId
      AND project_id = :projectId
)
LIMIT 1
```

그 후 st\_snaptogrid에서 유효하지 않은 폴리곤 값이 반환될 경우 유효하도록 변환해주기 위해 st\_buffer 함수를 사용했다. 이로써 다음과 같은 쿼리가 완성됐으며 여러 경우에 있어서 모두 정상 동작하는 것을 확인했다.

```sql
WITH snap_polygons AS (
    SELECT st_buffer((st_dump(st_snaptogrid(changed_area::geometry, :precision))).geom, 0.0) 
        AS polygon
    FROM change_detection_result
    WHERE scene_id = :sceneId
      AND project_id = :projectId
),
changed_area_view AS (
    SELECT st_multi(st_intersection(st_union(polygon), :viewport)) AS changed_area
    FROM snap_polygons
    WHERE st_intersects(polygon, :viewport)
),
fallback_area AS (
    SELECT COALESCE(
               st_multi(st_intersection(st_envelope(changed_area::geometry),:viewport)),
               ST_Multi(ST_GeomFromText('MULTIPOLYGON EMPTY'))
           ) AS fallback_area
    FROM change_detection_result
    WHERE scene_id = :sceneId
      AND project_id = :projectId
    UNION ALL
    SELECT ST_Multi(ST_GeomFromText('MULTIPOLYGON EMPTY'))
    WHERE NOT EXISTS (
        SELECT 1
        FROM change_detection_result
        WHERE scene_id = :sceneId
          AND project_id = :projectId
    )
    LIMIT 1
)
SELECT
    COALESCE(ca.changed_area, fa.fallback_area) AS changed_area,
    COALESCE(st_area(COALESCE(ca.changed_area, fa.fallback_area), FALSE) / 1000000, 0.0) 
        AS changed_area_size
FROM changed_area_view ca
LEFT JOIN fallback_area fa ON TRUE
```



테스트 코드로는 데이터가 존재하는 경우에 대해서만 테스트를 수행하여 배포하기 전에는 쿼리가 잘못되어 있다는 것을 발견하지 못 했었다. 앞으로는 테스트 코드를 작성할 때 예외 케이스를 생각하여 해당 케이스들이 모두 잘 동작하는지 꼼꼼하게 확인하는 것이 좋을 것 같다.



### 사이드 프로젝트

#### GitHub과 Jira 연동

GitHub에서 Repository의 Branch와 Commit, PR의 제목에 Jira 티켓 아이디를 올릴 경우 Jira의 티켓에 자동으로 할당되도록 설정이 가능하다는 것을 알게 되었다. 이처럼 설정할 경우 작업이 어떤 작업이였으며 무슨 작업을 했는지 파악하기 쉽기 때문에 GitHub과 Jira를 연동하는 설정 작업을 진행했다. 해당 설정을 구성하기 위해 방법을 찾아보고 다음과 같이 연동을 적용했으며 문서로 정리했다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/etc/github-jira" %}

#### Gitflow 정책 정리

현재는 사이드 프로젝트를 백엔드와 프론트가 한 명씩 존재하며 각 저장소가 분리되어 있으므로 사실상 혼자 작업을 수행한다. 그래서 Gitflow를 복잡하게 구성하지 않고 실제 서비스 배포를 위한 main 브랜치와 테스트 서버 배포를 위한 develop 브랜치로 나누고, develop 브랜치에서 Jira 티켓을 기준으로 브랜치를 생성하여 작업하는 것으로 결정했다.



#### CI/CD 구축

정책을 결정한 Gitflow를 기준으로 CI/CD 구축을 진행했다. 먼저 PR을 올렸을 때 수행할 빌드 테스트 구성 작업을 진행했으며, 해당 작업을 수행하면서 여러 버그가 발생하여 버그 수정 작업도 같이 진행했다.

처음으로 github workflow 파일을 작성해서 업로드 했을 때 특정 파일이 존재하지 않아서 빌드 에러가 발생했다. 이는 .gitignore에 out 디렉터리를 제거하도록 구성해놨는데, hexagonal architecture를 적용하면서 out 디렉터리를 생성하여 해당 디렉터리가 제거된 상태로 git에 올라간 것이 문제였다. 그래서 .gitignore에서 out 디렉터리를 제거하고 해당 파일들을 올려주니 해결되었다.

그 다음으로는 테스트 에러가 발생했다. 이는 설정 파일이 존재하지 않아서 발생한 문제로 기본 설정 파일을 git에 올려주지 않아서 발생하는 문제였다. 기본 설정 파일에는 공개되면 안되는 정보들이 존재하여 .gitignore를 통해 업로드 되지 않도록 막아서 발생한 것이다. src/main/resources에 존재하는 application.yaml 파일만 업로드 되지 않도록 설정해놨으므로 application.yaml 에서 비공개 값들을 테스트 값으로 변경하고 src/test/resources에 업로드하여 해결했다.

그 후에도 테스트가 실패하고 있으므로 추가적으로 해결을 진행할 예정이다.



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] 변화 탐지 영역 조회 API 성능 측정
* [ ] 사이드 프로젝트
  * [x] Github & Jira 연동
  * [x] Gitflow 정책 정리
  * [ ] CI/CD 구축
