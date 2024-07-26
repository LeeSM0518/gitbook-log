---
description: 일일 회고 3회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 😢 2024.07.26

## 할일 및 한일

* [x] 회사 업무
  * [x] 영상 입력 처리 실패 해결
* [x] 사이드 프로젝트
  * [x] Spring Event 학습



## 경험 및 배움

### 회사 업무

#### 영상 입력 처리 실패 해결

폐쇄망 환경의 기관에 납품되어 있는 시스템에서 영상을 입력하다가 실패했다는 얘기를 전달받았다. 두 Geometry 타입의 객체를 intersection 했을 때 self-intersection 이 되어 있는 경우 발생하는 에러였으며 에러 메시지는 다음과 같다.

```kotlin
Exception in thread "main" org.locationtech.jts.geom.TopologyException: found non-noded intersection between LINESTRING ( 120.65259965054962 31.285279777143465, 120.67974663557145 31.288251142146198 ) and LINESTRING ( 120.65273638403228 31.28526053475525, 120.6525866745773 31.28529679390149 ) [ (120.65263910444314, 31.2852840955574, NaN) ]
	at org.locationtech.jts.noding.FastNodingValidator.checkValid(FastNodingValidator.java:139)
	at org.locationtech.jts.geomgraph.EdgeNodingValidator.checkValid(EdgeNodingValidator.java:80)
	at org.locationtech.jts.geomgraph.EdgeNodingValidator.checkValid(EdgeNodingValidator.java:45)
	at org.locationtech.jts.operation.overlay.OverlayOp.computeOverlay(OverlayOp.java:229)
	at org.locationtech.jts.operation.overlay.OverlayOp.getResultGeometry(OverlayOp.java:181)
	at org.locationtech.jts.operation.overlay.OverlayOp.overlayOp(OverlayOp.java:84)
	at org.locationtech.jts.operation.overlay.snap.SnapIfNeededOverlayOp.getResultGeometry(SnapIfNeededOverlayOp.java:75)
	at org.locationtech.jts.operation.overlay.snap.SnapIfNeededOverlayOp.overlayOp(SnapIfNeededOverlayOp.java:37)
	at org.locationtech.jts.geom.GeometryOverlay.overlay(GeometryOverlay.java:76)
	at org.locationtech.jts.geom.GeometryOverlay.intersection(GeometryOverlay.java:119)
	at org.locationtech.jts.geom.Geometry.intersection(Geometry.java:1330)
	at org.jetbrains.kotlin.idea.scratch.generated.ScratchFileRunnerGenerated$ScratchFileRunnerGenerated.<init>(tmp.kt:15)
	at org.jetbrains.kotlin.idea.scratch.generated.ScratchFileRunnerGenerated.main(tmp.kt:20)
```



위 에러는 에러가 발생하는 케이스를 찾아서 테스트 코드를 작성하여 다음과 같이 해결하는 작업을 완료했었다.

```kotlin
import com.sia.inference.util.tryToMultiPolygon
import org.locationtech.jts.geom.GeometryFactory
import org.locationtech.jts.io.WKTReader

// JTS GeometryFactory 초기화
val geometryFactory = GeometryFactory()

val polygon1 =
    WKTReader().read("MULTIPOLYGON (((120.65259965054962 31.285279777143465, 120.67974663557145 31.288251142146198, 120.68831455911376 31.28918923620673, 120.70794961491976 31.282636365231323, 120.76374886330346 31.283832422390343, 120.78566214063716 31.282849157114605, 120.78727475541889 31.282211626764653, 120.7870154283358 31.28112971510409, 120.78159715214149 31.281396505875275, 120.7675993122942 31.282369818420804, 120.76620314875765 31.281048292763057, 120.7622268383381 31.282310090290256, 120.75562997396135 31.281939054121096, 120.74886389782327 31.28171655444499, 120.74540558497365 31.28169741706209, 120.73917835062895 31.28125748233043, 120.73308266356383 31.281259760653278, 120.70952760230696 31.279938436718577, 120.70172330921004 31.281048754102418, 120.690769367383 31.283502308101333, 120.68806834503596 31.285431202279767, 120.67784877635694 31.28510197877543, 120.66475600163066 31.284907044197734, 120.65301591965225 31.28423244481776, 120.65273638403228 31.28526053475525, 120.6525866745773 31.28529679390149, 120.6525866745773 31.28529679390149, 120.65259965054962 31.285279777143465)))").tryToMultiPolygon()
        .buffer(0.0)

// 두 번째 Polygon 생성 (사각형이 교차하는 형태)
val polygon2 =
    WKTReader().read("MULTIPOLYGON (((120.69573564330616 31.327309999189573, 120.69612595247793 31.327308370013657, 120.69718722428661 31.325717255890233, 120.69808270409025 31.325368971486622, 120.70169963540822 31.325569380113155, 120.70516017403206 31.325597389289385, 120.70689764247813 31.324660350571925, 120.75542191932003 31.329295587119677, 120.75550805594263 31.328123043584245, 120.72823987922153 31.325796147944697, 120.70711040566125 31.323744401744424, 120.70573841638927 31.324149119597184, 120.70488267408774 31.32470010686361, 120.69776330474302 31.32476569478398, 120.69647748854426 31.325444487565747, 120.69566285003579 31.326380456642895, 120.69573564330616 31.327309999189573)))").tryToMultiPolygon()
        .buffer(0.0)

// 두 Polygon의 교차점을 계산합니다.
val intersection = polygon1.intersection(polygon2)

```

```kotlin
POLYGON EMPTY
```

<details>

<summary>buffer(0.0) ??</summary>

코드에서 `buffer(0.0)`을 호출하는 부분은 JTS (Java Topology Suite) 라이브러리의 **`Geometry`** 객체에 대해 버퍼 연산을 수행하는 부분입니다.   **`buffer()`** 메서드는 주어진 지오메트리 주변에 지정된 거리만큼의 버퍼 영역을 생성합니다. 이 경우, 버퍼 거리로 `0.0`이 지정되어 있습니다.

#### **`buffer(0.0)`의 목적과 작동 원리:**

* **목적**: `buffer(0.0)`을 사용하는 주된 목적은 지오메트리의 간소화(simplification) 또는 정규화(normalization)를 수행하여 작은 기하학적 불규칙성이나 자기 교차(self-intersections) 같은 문제를 해결하는 것입니다. 이러한 문제들은 종종 지오메트리를 처리하거나 공간 분석을 수행할 때 오류의 원인이 됩니다.
* **작동 원리**: **`buffer(0.0)`** 호출은 지오메트리에 0의 거리만큼 버퍼를 추가합니다. 즉, 실제로 지오메트리의 크기를 변경하지 않습니다. 그러나 이 과정에서 JTS 라이브러리는 지오메트리의 내부 구조를 다시 계산하게 됩니다. 이는 지오메트리의 불규칙성을 제거하고, 깨끗하고 정확한 지오메트리 표현을 확보하는 효과가 있습니다. 특히, 자기 교차나 매우 가까운 선분들이 서로 '스냅(snap)'되어 더 일관성 있는 구조로 정렬될 수 있습니다.

#### **사용 사례:**

* **자기 교차 제거**: 폴리곤이나 라인스트링이 자기 자신과 교차하는 경우, 이를 해결하기 위해 사용될 수 있습니다.
* **작은 불규칙성 수정**: 지오메트리의 경계에 매우 작은 불규칙성이 있을 때, 이를 평활화하여 보다 정규화된 형태로 만들 수 있습니다.
* **로버스팅(robustness) 향상**: 공간 연산 중 발생할 수 있는 작은 오류나 불안정성을 줄여, 연산의 안정성을 향상시킬 수 있습니다.

`buffer(0.0)`는 지오메트리를 변경하지 않는 것처럼 보이지만, 실제로는 내부적으로 지오메트리를 '정리'하고, 오류 가능성을 줄이는 데 도움이 됩니다. 그러나 모든 경우에 이 방법이 적합한 것은 아니며, 특히 큰 지오메트리 데이터셋을 처리할 때는 성능 저하를 초래할 수 있으므로 주의가 필요합니다.

</details>



하지만 동일하게 문제가 발생되어 기관으로 출장가신 개발자분에게 확인을 요청했으나 정확한 문제를 알아내지 못 하셨다. 어떤 데이터를 사용해서 호출했을 때 문제가 발생하는지 알 수 없으므로 다른 방법으로 문제 해결을 진행했다.

intersection을 수행하기 전에 Geometry가 유효하지 않을 경우 buffor(0.0)를 사용하여 값을 변환한 후에 intersection 하도록 수정했었다. 이 부분이 문제가 발생하므로 해당 기능을 PostGIS를 사용하여 처리되도록 변경을 진행했다.

다음 네이티브 쿼리를 사용하여 intersecton을 수행하도록 변경한 후에 테스트 코드로 검증하고, 도커 이미지를 테스트 서버에 배포하여 영상을 넣었을 때 정상적으로 동작하는 것을 확인했다.

```sql
select st_intersection(
    st_makevalid(:geometry1), 
    st_makevalid(:geometry2)
) as geometry
```



다음주 월요일에 어떤 데이터가 문제가 발생하는지 확인한 후에 해당 데이터가 네이티브 쿼리로 정상동작 하는지 검증하고 서버를 업데이트 할 예정이다. 잘 마무리 되길 빈다..



### 개인 프로젝트

#### Spring Events 학습





## 개선 및 목표



