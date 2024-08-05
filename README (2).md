---
description: 일일 회고 9회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.02

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
  * [x] 백엔드 CI/CD 개선
* [x] 사이드 프로젝트
  * [x] 알림 기능 설계

## 경험 및 배움

### 회사 업무

#### 백엔드 CI/CD 개선

Github 자체 Runner로 gradle build를 수행하고 생성된 jar 파일을 캐시로 저장한 후, self-hosted Runner에서 해당 캐시를 다운로드 받아서 docker build와 push를 수행하도록 개선을 진행했다.&#x20;

다음 글에 따르면 캐시는 한 Repository에서 최대 10GB로 제한되지만 최대 캐시 스토리지에 도달하면 캐시 제거 정책에 따라 가장 오래된 캐시가 삭제되고 공간이 만들어진다. 또한 캐시는 아티팩트에 비해 비용이 저렴하므로 캐시를 사용하도록 수정했다.

{% embed url="https://docs.github.com/ko/actions/writing-workflows/choosing-what-your-workflow-does/caching-dependencies-to-speed-up-workflows#usage-limits-and-eviction-policy" %}

백엔드 CI/CD를 빠른 시일내로 개선하기 위해 문제가 정확하게 무엇이며 어떻게 수정하는 것이 좋을지를 제대로 정리하지 못 하고 작업을 진행했던 것 같다. 다음에는 문제 원인을 분석하고 개선할 방향을 정하는 시간을 정하고 진행하도록 하자.

### 사이드 프로젝트

#### 알림 기능 설계

요구사항을 정의하고 데이터 설계를 수행했다.

<table><thead><tr><th width="158">Name</th><th width="218">Column</th><th width="157">Data type</th><th>Key type</th><th width="87">Unique</th><th width="90">Not null</th><th>Note</th></tr></thead><tbody><tr><td>알림 식별자</td><td>notification_id</td><td>uuid</td><td>PK</td><td>O</td><td>O</td><td></td></tr><tr><td>알림 유형</td><td>notification_type</td><td>varchar</td><td></td><td></td><td>O</td><td>REVIEW(리뷰), INQUIRY(문의), ANNOUNCE(공지), PURCHASE(구매), SALES(판매), WITHDRAWAL(출금), COMMENT(댓글)</td></tr><tr><td>내용</td><td>content</td><td>jsonb</td><td></td><td></td><td>O</td><td></td></tr><tr><td>수신자</td><td>receiver_id</td><td>uuid</td><td>FK</td><td></td><td>O</td><td></td></tr><tr><td>생성 날짜</td><td>created_date</td><td>timestamp</td><td></td><td></td><td>O</td><td></td></tr><tr><td>확인 여부</td><td>confirmed</td><td>boolean</td><td></td><td></td><td>O</td><td>default: false</td></tr></tbody></table>

* 어떤 알림인지에 따라 내용이 변경될 수 도 있을 것으로 예상되어 구조를 유연하게 저장할 수 있도록 내용의 타입을 jsonb로 설정했다.
* 수신자는 공지 알림일 경우 모든 사용자가 확인해야 하므로 nullable로 설정하여 하나만 저장하려 했으나, 사용자의 확인 여부를 판단해야 하므로 not null로 설정했다.
* 확인 날짜는 사용자가 확인하지 않았을 경우에는 null로 설정하고 확인한 날짜를 저장하려 했으나, 확인한 날짜를 관리할 필요가 없을 것으로 판단되어 boolean으로 설정했다.

{% hint style="info" %}
_**PostgreSQL의 타입 중에 bool과 boolean의 차이 ?**_

PostgreSQL에는 bool과 boolean 타입이 있으나 bool은 boolean의 별칭을 의미한다.

`SELECT pg_typeof(false::bool)` 과 `SELECT pg_typeof(false::boolean)` 을 실행할 경우 둘 다 `boolean` 이라는 결과가 나오는 것을 통해 증명된다.
{% endhint %}



이처럼 설계한 것이 가장 좋은 방법이 아닐 수도 있으니, 다른 사람들은 어떻게 설계했는지 좀 더 조사한 후에 클래스 다이어그램 설계를 진행하자.



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
