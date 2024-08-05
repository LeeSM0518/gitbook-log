---
description: 일일 회고 10회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.03

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

* [x] 개인 학습
  * [x] 일일 회고 템플릿 수정
* [x] 사이드 프로젝트
  * [x] CORS 적용
  * [x] Paging 코드 수정

## 경험 및 배움

### 개인 학습

#### 일일 회고 템플릿 수정

기존의 일일 회고 작성 방식은 내가 무엇을 잘 하고 있으며 무엇을 고쳐야 하는지를 상기시키는 것이 부족하다고 생각이 들었다. KPT 회고 방식을 활용하여 지속할 것과 문제가 있는 것 그리고 문제를 개선하기 위해 시도할 것을 일일 회고 템플릿의 최상단에 작성해놨다. 일일 회고를 작성하면서 KPT를 매일 확인함으로써 내가 어떤 것을 유지하고 개선해야 하는지를 상기시킬 것이다.

### 사이드 프로젝트

#### CORS 적용

AWS를 활용하여 사이드 프로젝트를 배포해놨으나 프론트에서 API 호출했을 때 CORS 문제가 발생했다. CORS 문제를 해결하기 위해 다음과 같이 설정을 추가했다.

```kotlin
@Configuration
class CorsGlobalConfig : WebFluxConfigurer {

    override fun addCorsMappings(registry: CorsRegistry) {
        registry
            .addMapping("/**")
            .allowedOrigins("http://localhost:3000", "http://localhost:3001")
            .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
            .allowedHeaders("Authorization", "Content-Type", "X-Requested-With")
            .allowCredentials(true)
    }
}
```

위 코드는 개발 환경에서만 사용되어야 하므로 @Profile을 추가하여 개발 환경에만 적용될 수 있도록 CORS 설정을 수정할 예정이다.



#### Paging 코드 수정

사이드 프로젝트의 백엔드 서버를 배포하고 GET 요청을 수행하다가 버그를 발견하여 수정 작업을 진행했다. 코드를 확인해보니 다음과 같이 limit을 가져올 때 Pageable 객체로부터 pageSize만 조회하여 잘못된 값이 나오고 있었다.&#x20;

```kotlin
data class Paging(
    val value: Pageable,
) {
    constructor(
        page: Page,
        count: Count,
    ) : this(
        value = Pageable
            .ofSize(count.toIntCount())
            .withPage(page.toIntPage())
    )
    
    val offset = value.offset.toInt()
    val limit = value.pageSize
}
```



이를 해결하기 위해 테스트 코드를 작성하고, 테스트가 정상적으로 수행될 수 있도록 코드를 수정했다.

```kotlin
internal class PagingTest {

    @Test
    fun `Paging으로부터 offset과 limit을 가져올 수 있다`() {
        val list = listOf(1, 2, 3, 4)
        val paging1 = Paging(page = "1", count = "2")
        val paging2 = Paging(page = "2", count = "2")

        assertThat(list.subList(paging1.offset, paging1.limit)).containsAll(listOf(1, 2))
        assertThat(list.subList(paging2.offset, paging2.limit)).containsAll(listOf(3, 4))
    }

    @Test
    fun `페이지가 0 이하일 경우 예외가 발생한다`() {
        assertThrows(CommonError.INVALID_PAGE.exception) { Paging(page = "0", count = "2") }
    }

    @Test
    fun `개수가 0 이하이며 1000초과일 경우 예외가 발생한다`() {
        assertThrows(CommonError.INVALID_COUNT.exception) { Paging(page = "1", count = "1001") }
    }
}
```

```kotlin
data class Paging(
    val value: Pageable,
) {
    constructor(
        page: Page,
        count: Count,
    ) : this(
        value = Pageable
            .ofSize(count.toIntCount())
            .withPage(page.toIntPage() - 1)
    )

    val offset: Int = value.offset.toInt()
    val limit: Int = value.pageSize * (value.pageNumber + 1)
}

private fun Page.toIntPage() =
    runCatching {
        this.toInt().takeIf { it > 0 }
            ?: throw CommonError.INVALID_PAGE.exception
    }.getOrElse { throw CommonError.INVALID_PAGE.exception }

private fun Count.toIntCount() =
    runCatching {
        this.toInt().takeIf { it in 1..1000 }
            ?: throw CommonError.INVALID_COUNT.exception
    }.getOrElse { throw CommonError.INVALID_COUNT.exception }
```

위와 같이 페이징을 적용하는 것 보다 더 좋은 방법이 존재하는지 Webflux와 R2DBC를 사용할 때의 페이징 활용법을 찾아볼 예정이다.



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] 백엔드 Github Actions 개선
  * [ ] LWLock 발생 원인 및 해결 방법 분석
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
* [ ] 사이드 프로젝트
  * [ ] 알림 기능 설계
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] Webflux와 R2DBC에서의 페이징 활용법 조사
