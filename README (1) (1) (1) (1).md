---
description: 일일 회고 19회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.13

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
  * [x] 고객사 요청 작업 대응
  * [x] 신기능 디자인 리뷰 회의
* [x] 사이드 프로젝트
  * [x] 알림 읽음 상태 변경 테스트 구현

## 경험 및 배움

### 회사 업무

#### 고객사 요청 작업 대응

* 고객사의 AI 모델을 저장할 때 식별자를 숫자로 하는 것이 맞는지 고민
* 우리 쪽에서도 모델에 대한 식별자를 숫자로 하고 있음
* 고객사에서 직접 만든 모델에 대해 식별자를 숫자로 할 경우 해당 숫자를 앞으로 사용하지 못 함
* UUID를 사용하고 싶으나 AI 서버에서 식별자를 숫자로 처리하고 있어서 어려움이 있음
* _**우리 쪽에서 전혀 사용되지 않을 음수를 사용하는 것에 대해 제안 필요**_

#### 신기능 디자인 리뷰 회의

* 모델 재학습 신기능 구현에 대한 디자인 리뷰 회의 진행
* 많은 내용이 오갔지만 대용량 데이터셋을 어떻게 업로드 할 것 인지에 대한 추가 논의 필요
* _**대용량 파일을 어떻게 처리하는 것이 효율적일지 조사 필요**_



### 사이드 프로젝트

#### 알림 읽음 상태 변경 테스트 구현

* 알림 읽음 상태 변경 API를 "/notifications/{notificationId}/checked" 로 정의
  * 해당 API로 단일 알림과 모든 알림의 읽음 상태를 변경하도록 구현함
  * 통합 테스트를 구현하여 실행 해보니 테스트가 실패하여 원인 파악 필요

```kotlin
@Test
    fun `읽지 않은 모든 알림의 읽음 상태를 변경 요청할 수 있다`() = runTest {
        // given
        val memberId = expected.receiverId.toString()

        // when
        val count = webTestClient
            .patch()
            .uri("/notifications//checked")
            .accept(APPLICATION_JSON)
            .header(AUTHORIZATION, memberId)
            .exchange()
            .expectBody<CheckNotificationResponse>()
            .returnResult()
            .responseBody!!
            .count

        // then
        assertThat(count).isOne()
    }
```



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log#undefined-2))
  * [ ] 주기적으로 실행하는 로직에 Timer 적용
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
* [ ] 사이드 프로젝트
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
  * [ ] uuid v7 적용
  * [ ] 에러 로깅 적용
