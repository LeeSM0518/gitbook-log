---
description: 일일 회고 12회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# 🙂 2024.08.06

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
  * [x] Github Actions 팀즈 알림 수정
  * [x] AI 탐지 결과 저장 실패 해결
* [x] 사이드 프로젝트
  * [x] 알림 기능 설계
  * #### 설정값 변경 및 재배포

## 경험 및 배움

### 회사 업무

#### Github Actions 팀즈 알림 수정

Github Actions 에서 Teams API 호출이 다음과 같이 400 에러가 발생했다.

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

문제를 해결하기 위해 에러가 발생하는 명령을 로컬에서 실행하여 원인을 분석했다. 분석해보니 맨션하기 위한 사람들의 이메일과 이름을 HTTP Body에 등록해놨으나 등록한 사람을 맨션하여 알림을 하고 있지 않아서 발생한 문제였다. 에러 응답은 다음과 같다.

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

추가한 사람들을 body로 넣어서 호출하니 정상적으로 알림되는 것을 확인했다.

다음은 API 호출시 Body의 예시이다.

```json
{
  "type": "message",
  "attachments": [
    {
      "contentType": "application/vnd.microsoft.card.adaptive",
      "content": {
        "type": "AdaptiveCard",
        "body": [
          // 내용
          {"type": "TextBlock", "text": "cc. <at>Sangmin Lee</at> "}
        ],
        "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
        "version": "1.0",
        "msteams": {
          "entities": [
            {
              "type": "mention",
              "text": "<at>Sangmin Lee</at>",
              "mentioned": {
                "id": "nalsm0518@si-analytics.ai",
                "name": "Sangmin Lee"
              }
            }
          ]
        }
      }
    }
  ]
}
```

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Teams를 사용해서 API로 알림을 보내고 싶으면 다음과 같은 Teams 워크플로를 등록하여 사용하면 된다.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



#### AI 탐지 결과 저장 실패 해결

AI 탐지 결과 저장을 실패하여 로그를 확인해보니, AI 모델로 객체를 탐지하고 결과를 전달하는 서버에서 빈 리스트로 전달되는 것을 발견했다. AI 서버로부터 전달받은 결과를 GeoJson 구조의 객체로 변환하는데 이때 문제가 발생한 것이다.

AI 서버 개발자에게 해당 내용을 전달하여 빈 리스트로 응답되는 것이 잘못된 것을 확인하여, AI 서버 개발자가 이를 해결하여 재배포 한 후 테스트를 해 봤을 때 다시 정상화 된 것을 확인했다.



### 사이드 프로젝트

#### 알림 기능 설계

다음과 같이 간단하게 클래스 다이어그램을 설계했으며 이를 토대로 구현을 진행할 예정이다.

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



#### 설정값 변경 및 재배포

프론트에서 로그인 연동중에 에러가 발생하여 확인해보니, 프론트에서 카카오로 전송한 redirectUri와 백엔드에서 카카오로 전송한 redirectUri가 다를 경우 에러가 발생하는 것으로 확인됐다. 백엔드에서 프론트의 redirectUri에 맞춰서 변경한 후에 재배포하여 테스트해보니 정상적으로 동작했다.

OAuth2를 연동할 때 외부 서비스로부터 토큰 발급 및 회원 정보를 조회하는 로직을 프론트가 아닌 백엔드에서 구현을 하여 이처럼 번거로운 문제가 발생하게 됐다. 다른 서비스들에서는 어떻게 OAuth를 연동하는지 조사해보고 비교해 볼 필요가 있다.



## 앞으로 할 일

* [ ] 회사 업무
  * [ ] LWLock 발생 원인 및 해결 방법 분석
  * [ ] Github Actions 추가 개선 (ref. [백엔드 Github Actions 개선](https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-log#undefined-2))
* [ ] 개인 학습
  * [ ] AOP의 Joinpoint 분석
  * [ ] @Transactional 동작원리 학습
  * [ ] Webflux와 R2DBC에서의 페이징 활용법 조사
* [ ] 사이드 프로젝트
  * [x] 알림 기능 설계
  * [ ] Swagger Docs 보완
  * [ ] @Profile 적용
  * [ ] 알림 기능 구현
