---
description: 일일 회고 18회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.12

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
  * [x] 신기능 개발 공수 산정
* [x] 개인 학습
  * [x] WebFlux와 R2DBC를 활용한 페이징 사용 조사
* [x] 사이드 프로젝트
  * [x] 알림 읽음 상태 변경 구현

## 경험 및 배움

### 회사 업무

#### 신기능 개발 공수 산정

신기능 개발에 대해 작업이 필요한 항목들을 정리하고 개발 공수를 산정하는 작업을 완료했다.

공수를 예측할 때 자신이 생각하는 것 보다 1.5를 곱하여 어느정도 여유를 두어야 하는 것으로 알고 있지만 1.5를 곱하지는 않았다. 왜냐하면 신기능을 개발하고 납품하기 까지 3달이 남았기 때문에 1.5를 곱할 경우 3달을 훨씬 초과하여 여유를 두지는 못 했다.

일정이 너무 촉박하여 미리 설계하고 개발하고 싶지만 기획과 디자인이 완성된 상태가 아니라 고민이 많이 되고 있다. 기획과 디자인이 완료되는 것은 9월 초로 잡혀 있지만 현재 어느정도 기획과 디자인이 나와 있으므로 이를 바탕으로 설계와 개발을 진행할 예정이다.

### 개인 학습

#### WebFlux와 R2DBC를 활용한 페이징 사용 조사

WebFlux와 R2DBC에서 Pagination을 사용하는 Baeldung 글을 보고 다음 개인 블로그에 정리를 완료했다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-tech/spring/pagination" %}

Baeldung 글에 Pagination 관련 개념과 예제가 간단하게만 적혀 있어서, 예제를 그대로 수행하지 않고 기존에 만들고 있던 알림 서비스에 Pagination을 적용하면서 예제를 익혔다. 핵심은 다음과 같다.

1. CoroutineCrudRepository에서 데이터를 조회할 때 pageable로 조회하며 Flow로 반환하는 것

```kotlin
@Repository
interface NotificationRepository : CoroutineCrudRepository<NotificationEntity, UUID> {

    suspend fun findAllByReceiverIdOrderByNotifiedDateDesc(
        receiverId: UUID,
        pageable: Pageable,
    ): Flow<NotificationEntity>

}
```



2. PageRequest.of 를 사용하여 Pageable 객체를 생성하는 것

```kotlin
data class GetNotificationsRequest(
    val memberId: UUID,
    private val page: Int,
    private val size: Int,
) {
    val pageable: Pageable = PageRequest.of(page, size)
}
```



3. PageImpl 객체를 생성하여 Page 객체로 반환하는 것

```kotlin
@Service
class NotificationQueryService(
    private val loadNotificationPort: LoadNotificationPort,
) : GetNotificationUseCase {

    override suspend fun getNotifications(request: GetNotificationsRequest): Page<NotificationResponse> {
        val notifications = loadNotificationPort.loadNotifications(request).map { it.toResponse() }
        val count = loadNotificationPort.loadCount(request.memberId)
        return PageImpl(notifications.toList(), request.pageable, count)
    }

}
```



4. Page 객체를 사용자 정의 객체로 변환한 후에 사용자에게 응답하는 것

```kotlin
data class PageResponse<T>(
    val data: List<T>,
    val pageable: PageableResponse,
) {
    companion object {
        fun <T> Page<T>.toResponse() =
            PageResponse(
                data = this.content,
                pageable = this.toPageable(),
            )
    }

    data class PageableResponse(
        val totalPages: Int,
        val totalElements: Long,
    ) {
        companion object {
            fun Page<*>.toPageable() =
                PageableResponse(
                    totalPages = this.totalPages,
                    totalElements = this.totalElements
                )
        }
    }
}
```



위 내용들을 기반으로 알림 서비스에 적용했으며 다른 사이드 프로젝트에도 적용하면 좋을 것 같다.



### 사이드 프로젝트

#### 알림 읽음 상태 변경 구현

알림 서비스에 알림 읽음 상태를 변경하는 기능을 구현하는 것을 진행했다. 이 기능을 구현하면서 딱히 어려운 점은 없었으나 API를 어떻게 구성하는 것이 좋을지 고민이 되었다.&#x20;

처음에는 단일 알림만 읽음 상태로 변경하는 API와 요청자의 읽지 않은 모든 알림의 읽음 상태를 변경하는 API를 나눠서 개발하는 것을 생각했다. 하지만 두 API의 처리 로직이 유사하여 굳이 API를 두 번 만들지 않고 요청 값에 따라 하나의 알림 상태를 변경하거나 모든 알림 상태를 변경하도록 구성이 가능할 것으로 판단되어 이와 같이 작업을 진행했다.

다음과 같이 API를 구성하여 경로에 알림 식별자가 존재할 경우 해당 알림의 상태만 변경하고 알림 식별자가 존재하지 않을 경우 모든 알림의 상태를 변경하도록 구현했다.

```kotlin
@PatchMapping("/notifications/{notificationId}/checked")
suspend fun checkNotifications(
    @RequestHeader(AUTHORIZATION)
    authorization: String,
    @PathVariable("notificationId", required = false)
    notificationId: UUID?,
): CheckNotificationResponse {
    val memberId: UUID = getMemberUseCase.getMemberIdBy(authorization)
    val request = CheckNotificationsRequest(memberId, notificationId)
    val count: Int = modifyNotificationUseCase.check(request)
    return CheckNotificationResponse(count)
}
```

추후에도 수정이나 삭제 작업을 단일로 하거나 모두로 할 경우 이 방법을 사용하면 될 것 같다.



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log#undefined-2))
  * [ ] 주기적으로 실행하는 로직에 Timer 적용
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
  * [x] Webflux와 R2DBC에서의 페이징 활용법 조사
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용
  * [ ] 에러 로깅 적용
