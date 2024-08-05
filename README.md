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
* [ ] 사이드 프로젝트
  * [ ] CORS 적용
  * [ ] Paging 코드 수정

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
