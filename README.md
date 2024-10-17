---
description: 일일 회고 26회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.10.16

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
* [x] 사이드 프로젝트
  * [x] 알림 기능 구현

## 경험 및 배움

### 회사 업무

#### 대량 변화 탐지시 조회 성능 측정

변화탐지 AI 모델을 사용해서 15000x15000 크기의 영상 두 개를 비교했을 때 약 **100MB 크기의 탐지 결과**가 저장되는 것을 확인했다. 100MB 탐지 결과를 프론트에서 그대로 보여주면 **조회 속도가 매우 느릴 것으로 예상**됐다. 변화 탐지 결과를 조회하는 기능이 정상적으로 동작할 수 있는지 확인하기 위해 **조회 성능 측정**을 진행했다.

먼저 랜덤하게 변화 탐지 결과를 생성하여 약 **20MB 크기로 테스트 데이터를 구성**했다. 그 후 테스트 서버의 데이터베이스에 **테스트 데이터를 저장**한 후 해당 결과를 조회하는 API를 호출했다. API가 1분 이상 소요되어 **504(Gateway Timeout) 에러가 발생**했다.

정확하게 어느 로직에서 오랜 시간이 소요되는지 확인하기 위해 20MB 크기의 변화 탐지 결과를 저장하고 조회하는 **테스트 코드를 작성한 후 소요 시간을 확인**했다. Repository에서 쿼리를 통해 **데이터베이스에서 데이터를 불러올 때 50초 이상 소요**되는 것을 확인했다.&#x20;

20MB 크기의 데이터를 데이터베이스에서 조회하는 것이 단순히 크기가 커서 오래 걸리는 것인지 아니면 **Geometry 정보이기 때문에 느린 것인지 확인이 필요**하다. 20MB인 일반 문자열을 조회했을 때 빠르게 조회될 경우 일반 문자열로 조회하도록 변경하기 위함이다.



### 사이드 프로젝트

#### 알림 기능 구현

사용자가 구매한 전자책을 리뷰하거나 리뷰에 댓글을 작성할 때 **이벤트를 발행하여 알림을 저장하도록 구현**하는 것을 진행했다. 이벤트 발행 로직은 대부분 비슷하며 다음과 같이 **데이터를 조회하고 이벤트를 생성한 후 발행**하도록 구현했다.

```kotlin
@Component
class ReviewEventService(
    private val memberService: MemberService,
    private val ebookService: EbookService,
    private val publisher: ApplicationEventPublisher,
) {

    suspend fun publish(review: Review) {
        val member = memberService.findById(review.writerMemberId)
        val ebook = ebookService.findById(review.ebookId)
        val createReviewEvent = CreateReviewEvent(
            reviewId = review.id,
            reviewerName = member.nickname,
            ebookId = ebook.id,
            ebookTitle = ebook.title,
            writtenDate = review.writtenDate,
            receiverId = ebook.sellingMemberId
        )
        publisher.publishEvent(createReviewEvent)
    }
}
```

이처럼 서비스 객체가 다른 서비스 객체를 호출하도록 했으며, Spring의 ApplicationEventPublisher 객체를 활용해 이벤트를 발행했다. 유지보수 측면에서 더 좋은 코드 구조를 고민해봤으나 **먼저 개발을 완료하고 배포한 후에 리팩토링을 진행하기로 결정**했다.

&#x20;그 후, 알림을 저장하고 조회하는 부분을 조금 수정했다. 기존에는 알림 내용이 어떤 알림인지에 따라 달라질 수 있으므로 알림 객체를 Json으로 변환한 후 Json을 String으로 DB에 저장하도록 구현했다. 하지만 String으로 프론트에 전달할 경우 값을 다루기 힘들 것으로 예상되어 **Map 타입으로 저장하고 응답하도록 수정**을 진행했다.

다음과 같이 **두 컨버터를 추가하고 R2DBC 설정 클래스에 등록해준 후 저장 타입을 변경**했다. 기존의 테스트 코드를 돌려보니 정상 동작하는 것을 볼 수 있었다.&#x20;

```kotlin
@Component
@ReadingConverter
class JsonToMapConverter(
    private val objectMapper: ObjectMapper
) : Converter<Json, Map<String, Any>> {

    override fun convert(source: Json): Map<String, Any> =
        objectMapper.readValue(source.asString())
}
```

```kotlin
@Component
@WritingConverter
class MapToJsonConverter(
    private val objectMapper: ObjectMapper
) : Converter<Map<String, Any>, Json> {

    override fun convert(source: Map<String, Any>): Json =
        Json.of(objectMapper.writeValueAsString(source))
}
```

```kotlin
@Configuration
@EnableR2dbcRepositories
class R2dbcConfiguration(
    private val databaseConfig: DatabaseConfig,
    private val customConverters: List<Converter<*, *>>,
) : AbstractR2dbcConfiguration() {

    override fun connectionFactory(): ConnectionFactory =
        ConnectionFactories.get(
            ConnectionFactoryOptions
                .builder()
                .option(ConnectionFactoryOptions.DRIVER, databaseConfig.driver)
                .option(ConnectionFactoryOptions.PROTOCOL, databaseConfig.protocol)
                .option(ConnectionFactoryOptions.HOST, databaseConfig.host)
                .option(ConnectionFactoryOptions.PORT, databaseConfig.port.toInt())
                .option(ConnectionFactoryOptions.DATABASE, databaseConfig.database)
                .option(ConnectionFactoryOptions.USER, databaseConfig.username)
                .option(ConnectionFactoryOptions.PASSWORD, databaseConfig.password)
                .build()
        )

    override fun r2dbcCustomConversions(): R2dbcCustomConversions =
        R2dbcCustomConversions(storeConversions, customConverters)

    @Bean
    fun initializer(): ConnectionFactoryInitializer =
        ConnectionFactoryInitializer()
            .apply {
                setConnectionFactory(connectionFactory())
                setDatabasePopulator(ResourceDatabasePopulator(ClassPathResource("schema.sql")))
            }
}
```

```kotlin
typealias NotificationNote = Map<String, String>
```

그 다음으로는 이벤트를 발행한 것이 알림으로 잘 저장되었는지 확인하는 테스트 코드를 작성할 것이다.



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] 대량 변화 탐지시 조회 성능 측정
    * [ ] 일반 문자열로 조회시 속도 확인
  * [ ] 대량 변화 탐지시 조회 성능 개선
* [ ] 사이드 프로젝트
  * [ ] 알림 기능 테스트 구현
