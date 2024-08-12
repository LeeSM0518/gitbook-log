---
description: 일일 회고 16회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.10

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

* [x] 사이드 프로젝트
  * [x] 알림 생성 테스트 구현
  * [x] 실시간 알림 조회 구현

## 경험 및 배움

### 사이드 프로젝트

#### 알림 생성 테스트 구현

알림 생성 기능 구현을 완료하고 기능이 정상적으로 동작하는지 확인하기 위한 테스트 코드 작성을 진행했다.&#x20;



이벤트 리스너를 테스트하기 위해 이벤트를 발행한 후 리스너에서 해당 이벤트가 처리가 완료되어 동일한 내용으로 알림이 저장 됐는지 확인했다. 하지만 알림 발행이 완료되고 알림을 조회했을 때 알림이 존재하지 않는 것으로 확인됐다.



원인을 파악해보니 리스너에서 @EventListener를 사용할 때 메서드가 suspend 일 경우 이벤트를 수신받지 못 하는 것으로 판단됐다. 이를 해결하기 위해 suspend를 제거하고 메서드에서CoroutineScope를 사용하도록 수정하니 정상적으로 동작하는 것을 확인했다.

```kotlin
@Component
class NotificationEventListener(
    private val createNotificationUseCase: CreateNotificationUseCase,
) {

    @EventListener
    fun consumeCreateDomainEvent(event: CreateNotificationEvent): Job =
        CoroutineScope(Dispatchers.IO).launch {
            val request: CreateNotificationRequest = event.toCreateNotificationRequest()
            createNotificationUseCase.create(request)
        }

}
```



그 다음으로 발생한 문제는 이벤트 발행 후 곧바로 데이터를 조회할 경우 데이터가 조회되지 않는 문제가 발생했다. 이벤트를 발행한 후에 delay 함수를 사용하여 100ms를 지연시켜 테스트가 성공하는 것을 확인했다. delay로 검증을 지연시키는 것 이외에 좋은 테스트 방법을 찾아봤을 때 썩 괜찮은 방법이 발견되지는 않았다. 추후에 괜찮은 테스트 방법을 찾게 되면 적용해보도록 하자.

```kotlin
@Test
fun `도메인 생성 이벤트를 처리할 수 있다`(): Unit = runBlocking {
    // given
    val expected = CreateReviewEvent(
        receiverId = UUID.randomUUID(),
        reviewId = UUID.randomUUID(),
        reviewerName = "reviewerName",
        postId = UUID.randomUUID(),
        postTitle = "postTitle",
        writtenDate = Instant.now(),
    )

    // when
    applicationEventPublisher.publishEvent(expected)
    delay(100)

    // then
    val notifications = notificationRepository.findAll().toList()
    val actual = notifications[0]

    assertThat(notifications.size).isOne()
    assertThat(actual.content).isEqualTo(expected.content)
    assertThat(actual.note).isEqualTo(expected.createNote())
    assertThat(actual.receiverId).isEqualTo(expected.receiverId)
}
```



회고 글을 작성하면서 코드를 살펴보다 보니 CoroutineScope를 통해 스코프를 생성하고 launch로 코루틴을 실행할 경우 예외가 발생했을 때 에러가 로깅되지 않을 것으로 판단됐다. 이를 위해 로깅도 추가해서 적용해보도록 하자.



#### 실시간 알림 조회 구현

알림 생성 기능의 테스트 코드 구현을 마치고 실시간 알림 개수를 조회 기능을 구현했다.

실시간 조회를 위해 처음 사용해보는 Spring SSE를 사용했으나, 구현 방식은 기존의 REST 구현 방식과 동일하여 쉽게 구현할 수 있었다.

```kotlin
@RestController
class NotificationRouter(
    private val getMemberUseCase: GetMemberUseCase,
    private val getNotificationUseCase: GetNotificationUseCase,
) {

    @GetMapping("/notifications/count")
    suspend fun streamCountOfUncheckedNotifications(
        @RequestHeader(AUTHORIZATION)
        authorization: String,
    ): Flow<ServerSentEvent<StreamCountResponse>> =
        getMemberUseCase
            .getMemberIdBy(authorization)
            .let { memberId ->
                interval(streamIntervalDuration)
                    .asFlow()
                    .map { getCountOfUncheckedNotifications(memberId) }
            }

    private suspend fun getCountOfUncheckedNotifications(memberId: UUID): ServerSentEvent<StreamCountResponse> {
        val count: Long = getNotificationUseCase.getCountOfUncheckedBy(memberId)
        val response = StreamCountResponse(count)
        return ServerSentEvent
            .builder<StreamCountResponse>()
            .id(memberId.toString())
            .event("streamCountOfUncheckedNotifications")
            .retry(retryDuration)
            .data(response)
            .build()
    }

    companion object {
        private val streamIntervalDuration = Duration.ofSeconds(1)
        private val retryDuration = Duration.ofSeconds(10)
    }
}
```



테스트 코드 또한 기존의 REST 방식과 유사하여 쉽게 구현하고 검증할 수 있었다. 일반적인 REST 방식과 다른 점은 WebTestClient로 HTTP 요청을 전송할 때 Accept 헤더로 text/event-stream를 사용하는 것 외에는 거의 동일했다.

```kotlin
@Test
fun `실시간으로 읽지 않은 알림의 개수를 조회할 수 있다`() = runTest {
    // given
    val memberId = expected.receiverId.toString()

    // when
    val eventStream = webTestClient
        .get()
        .uri("/notifications/count")
        .accept(MediaType.TEXT_EVENT_STREAM)
        .header(HttpHeaders.AUTHORIZATION, memberId)
        .exchange()
        .expectStatus().isOk
        .returnResult(ServerSentEvent::class.java)
        .responseBody
        .blockFirst()!!

    // then
    val streamCountResponse = eventStream.data() as LinkedHashMap<*, *>
    assertThat(streamCountResponse["countOfUncheckedNotification"]).isEqualTo(1)
}
```



아직은 별다른 문제가 없지만 프론트와 연동을 진행하거나 배포를 하여 운영을 하게 되면 여러 문제가 있을 것으로 예상된다. 추후에 프론트와 연동을 진행하면서 좀 더 살펴보도록 하자.



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log#undefined-2))
  * [ ] 주기적으로 실행하는 로직에 Timer 적용
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
  * [ ] Webflux와 R2DBC에서의 페이징 활용법 조사
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용
  * [ ] 에러 로깅 적용
