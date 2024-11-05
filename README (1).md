---
description: 일일 회고 41회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.11.03

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

### 사이드 프로젝트

#### 카테고리와 인증 API에 Swagger 적용

Swagger는 라이브러리를 추가만 하면 구현되어 있는 API들을 기반으로 API Docs를 만들어준다. 하지만 해당 API가 어떤 API인지나 요청 값이 필수인지 아닌지는 직접 설정해줘야 API Docs에 반영되므로 모든 API에 Swagger 적용이 필요하다.&#x20;

모든 API에 Swagger를 적용하기 위해 간단한 카테고리 API부터 적용을 진행했다. 카테고리 API에 Swagger를 진행하다 보니 요구사항이 변경되어 API가 변경되어야 했으나 아직 변경되지 않은 API를 발견할 수 있었다. 카테고리 조회 API를 변경된 요구사항에 맞게 수정한 후 Swagger를 적용하여 작업을 마쳤다. 이처럼 모든 API에 Swagger를 적용하면서 API를 보완해 나가면 좋을 것 같다.

{% embed url="https://github.com/LeeSM0518/devooks/pull/17" %}

그 다음으로 인증 API에 Swagger 적용을 진행했다. 인증 API에는 로그인과 로그아웃, 토큰 재발급 API들에 미리 Swagger 적용이 되어 있었으며, 이메일 확인 API만 적용하면 되므로 간단한 작업이였다. 기존에 작업해 둔 것은 AuthController에 Swagger를 반영하여 실제 로직과 API Docs를 위한 로직이 동시에 존재하여 코드가 한눈에 들어오지 않았다. 그래서 Swagger 로직을 AuthControllerDocs 인터페이스 분리하고 AuthController는 인터페이스를 구현하는 형태로 리팩토링을 수행했다. 그 후 이메일 확인 API에 Swagger를 적용하여 작업을 마쳤다.

{% embed url="https://github.com/LeeSM0518/devooks/pull/19" %}

## 해야 할 일

* [ ] 사이드 프로젝트
  * [ ] 테스트 CI 성능 개선
  * [x] INVALID\_EMAIL 예외 메시지 오타 수정
  * [ ] Flyway 적용
