---
description: 일일 회고 33회차
cover: .gitbook/assets/Frame 85 (1).png
coverY: 0
---

# ✏️ 2024.10.24

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

### 회사 업무

#### GDAL 테스트 환경 구성

GDAL 라이브러리를 사용해서 영상을 읽고 특정 작업을 수행하는 코드를 테스트하기 위해 테스트 환경을 구성하는 작업을 진행했다. GDAL 테스트 환경을 구축하는 이유는 GDAL 라이브러리가 C++로 구성되어 있어서 JNI로 변환한 라이브러리를 JDK에 추가한 후에 테스트가 가능하기 때문이다. 이처럼 GDAL 라이브러리를 로컬에 구성하는 것은 까다로워서 IntelliJ에서 Docker로 테스트를 수행하기 위한 Docker 이미지 구축 작업을 진행했다.

Docker Image를 구성하고 Intellij에서 해당 이미지로 테스트를 수행해봤다. 성공적으로 테스트가 수행되지만 테스트가 완료되는데 매번 약 5분이 소요됐다. 테스트시 매번 라이브러리를 설치해오기 때문에 오래 걸리는 것으로 확인됐다. 이를 개선하기 위해 미리 라이브러리를 설치해놓은 이미지를 새로 빌드하고 사용해봐도 동일하게 라이브러리를 설치해오는 것으로 확인됐다.

테스트 실행시 라이브러리를 설치하지 않고 기존 라이브러리를 사용하도록 하는 방법을 좀 더 찾아보고 개선할 예정이다.



### 개인 학습

#### 자료구조 학습 (Set, Graph)

Set은 많이 사용해 본 자료구조이며 이해하기 쉽다. Set 내용을 간단하게 정리한 후 관련 문제를 풀어봤으며 이는 다음 문서에 정리해놨다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/data-structure/set" %}

Graph는 기본 개념은 간단하지만 구현하는 것과 탐색하는 방식이 단순하지 않아서 해당 개념과 구현 코드를 같이 정리했다. 그 후 관련 문제를 풀어본 뒤 다음 문서에 문제 내용과 코드를 같이 작성해놨다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/data-structure/graph" %}



#### 알고리즘 학습 (Backtracking)

백트래킹 알고리즘은 단순하게 가능성이 없는 곳은 되돌아간다는 단순한 개념을 내포하고 있지만, 어떠한 상황과 값이 가능성이 없는 것인지를 찾아내는 것이 어렵다. 개념은 간단하게 정리한 후 관련 문제를 여러 개를 풀어보면서 깊게 이해를 해야할 것 같다. 개념과 관련 문제를 푼 내용은 다음 문서에 정리해놨다.

{% embed url="https://jimmyblog.gitbook.io/jimmys-blog/jimmys-tech/algorithm/backtracking" %}



## 해야 할 일

* [ ] 회사 업무
  * [ ] 변화 탐지 영역 조회 API 성능 측정
  * [ ] GDAL 테스트 환경 개선
* [ ] 사이드 프로젝트
  * [ ] CI/CD 구축
  * [ ] 테스트 CI 성능 개선
