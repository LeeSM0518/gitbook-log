---
description: 일일 회고 4회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ☺️ 2024.07.28

## 할일 및 한일

* [x] 개인 공부
  * [x] Spring Events 학습
  * [x] Spring AOP 학습

## 경험 및 배움

### 개인 공부

#### Spring Events 학습

Spring Events에 대해 학습을 완료하여 다음 링크에 내용을 정리해뒀다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-tech/spring/spring-events" %}

Spring Events를 학습하면서 _@TransactionalEventListener_ 를 사용하면 트랜잭션에 할당되어 이벤트가 처리되는 것이 있었다. 이 어노테이션을 사용할 경우 정확하게 어떻게 처리되는 것인지는 이해가 잘 되지 않아서, 이해를 돕기 위해 @Transactional 관련 글을 찾아서 해당 어노테이션의 동작 방식을 살펴볼 예정이며 실제 예시를 하나 구현해서 테스트를 해볼 예정이다.



#### Spring AOP 학습

사이드 프로젝트에서 알림 기능을 Spring Events 와 Spring AOP를 활용하여 구현해보기 위해 Spring AOP 학습을 진행했다.&#x20;

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/v/jimmys-tech" %}

AOP 학습을 완료하고 고민을 해보니 joinpoint에서 인자 값을 가져올 수 있는지 확인해볼 예정이다. 그 이유는 사용자가 댓글을 남긴 글의 작성자에게 알림을 보내기 위해서는 어떤 사용자가 어떤 작성자에게 알림을 보내야하는지 알아야 하므로 joinpoint에서 사용한 값을 알아야 하기 때문이다.

## 개선 및 목표

* [ ] 회사 업무
  * [ ] 백엔드 Github Actions 개선
* [ ] 개인 학습
  * [ ] @Transactional 분석
  * [ ] AOP의 Joinpoint 분석
