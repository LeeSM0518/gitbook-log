---
description: 일일 회고 20회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.14

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
  * [x] LWLock 발생 원인 및 해결 방법 분석
  * [x] 주기적으로 실행하는 로직에 Timer 적용
* [x] 사이드 프로젝트
  * [x] 알림 읽음 상태 변경 테스트 구현

## 경험 및 배움

### 회사 업무

#### LWLcok 발생 원인 및 해결 방법 분석

* [29CM 팀의 PostgreSQL Autovacuum 장애 대응기 글](https://medium.com/29cm/postgresql-autovacuum-%EC%9E%A5%EC%95%A0-%EB%8C%80%EC%9D%91%EA%B8%B0-1-8284955c0193)을 보고 LWLock 발생 원인을 유추할 수 있게 됨
  * 회사 시스템에 특정 테이블을 주기적으로 초기화하고 데이터를 다시 넣는 스케줄링이 있음
  * 테이블이 초기화될 경우 dead tuple이 발생하여 Autovacuum이 동작함
  * _**vacuum이 실행되면서 block된 row들이 생겨 LWLock이 발생하게 됨**_
* 위 내용은 추측이므로 문제가 재현될 경우 명확한 원인 파악 필요

#### 주기적으로 실행하는 로직에 Timer 적용

* 스케줄링을 10초에 한 번씩 실행되어 시스템이 전반적으로 느려지는 문제 발생
* 스케줄링을 하루에 한 번씩 실행되도록 수정이 요구됨
* 기존의 스케줄링 로직은 서버가 실행될 경우 바로 스케줄링을 실행하는 로직이였음
  * while (true) 의 실행문에서 delay를 추가한 로직
* 사용자가 사용하는 시간과 스케줄링이 수행되는 시간을 완전히 분리하기 위해 자정 시간에 실행되도록 수정
  * java.util.Timer 객체를 활용하여 자정 시간에 실행되도록 수정

```kotlin
private const val SCENE_LABEL_VIEW_CACHE_TIME = 24 * 60 * 60 * 1000L

private fun cacheSceneLabelView() {
    val timer = Timer()
    val midnight = getNextMidnight()
    logger.info { "SceneLabelView 업데이트 스케줄링 시작 (한국 시간 기준 매일 자정에 수행)" }
    timer.scheduleAtFixedRate(midnight, SCENE_LABEL_VIEW_CACHE_TIME) {
        CoroutineScope(Dispatchers.IO).launch {
            logger.info { "SceneLabelView 업데이트 시작" }
            runCatching {
                injector().get<SceneLabelViewRepository>().use { it.refresh() }
            }.onSuccess {
                logger.info { "SceneLabelView 업데이트 완료" }
            }.onFailure {
                logger.error(it) { "SceneLabelView 업데이트 실패" }
            }
        }
        delay(Duration.ofSeconds(SCENE_LABEL_VIEW_CACHE_TIME))
    }
}

private fun getNextMidnight(): Date {
    val calendar = Calendar.getInstance(TimeZone.getTimeZone("Asia/Seoul"))
    calendar.set(Calendar.HOUR_OF_DAY, 0)
    calendar.set(Calendar.MINUTE, 0)
    calendar.set(Calendar.SECOND, 0)
    calendar.set(Calendar.MILLISECOND, 0)

    if (calendar.time.before(Date())) {
        calendar.add(Calendar.DAY_OF_MONTH, 1)
    }

    return calendar.time
}
```



### 사이드 프로젝트

#### 알림 읽음 상태 변경 테스트 구현

* 하나의 API로 단일 상태 변경과 전체 상태 변경을 수행하려 했으나 테스트를 실패함
  * API를 /notifications/{notificationId}/checked 로 정의함
  * /notifications//checked 와 /notification/{UUID}/checked로 두 가지의 경우를 처리하려 했음
* PatchMapping에 path 값으로 /notifications/{notificationId}/checked 와 /notifications/checked로 두 가지의 경우를 처리할 수 있게 수정함
  * 테스트 코드로 정상 동작하는 것 확인함

```kotlin
@PatchMapping(path = ["/notifications/{notificationId}/checked", "/notifications/checked"])
suspend fun checkNotifications() {}
```

```kotlin
// 읽지 않은 모든 알림의 읽음 상태 변경 테스트
val count = webTestClient
            .patch()
            .uri("/notifications/checked")
            // ...

// 읽지 않은 특정 알림의 읽음 상태 변경 테스
val count = webTestClient
            .patch()
            .uri("/notifications/${expected.id}/checked")
            // ...
```



## 앞으로 할 일

* [ ] 회사 업무
  * [x] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log/daily-log-2024/2024.08.05#github-actions))
  * [x] 주기적으로 실행하는 로직에 Timer 적용
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용
  * [ ] 에러 로깅 적용
