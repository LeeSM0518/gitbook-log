---
description: 일일 회고 15회차
cover: .gitbook/assets/Frame 77.png
coverY: 182.44271412680757
---

# 😄 2022.02.17

## 할일 및 한일

* [x] `배터리 관제 시스템`의 사이트 관리 페이지 구현 (50%)

## 경험 및 배움

### `배터리 관제 시스템`의 사이트 관리 페이지 구현

오늘은 사이트 관리 페이지의 **사이트 등록 기능**을 구현했다. 해당 기능은 사용자가 사이트 관련 정보들을 입력하도록 하고 주소 및 위도, 경도 정보는 **Daum 우편번호 API와 Kakao 로컬 API**를 통해 입력되도록 구현 완료했다.



{% embed url="https://postcode.map.daum.net/guide" %}
Daum 우편번호 API
{% endembed %}

{% embed url="https://developers.kakao.com/docs/latest/ko/local/dev-guide" %}
Kakao 로컬 API
{% endembed %}



사이트 등록 기능을 금방 구현할 줄 알았으나, Daum 우편번호 API를 연동하는데 생각보다 오랜 시간이 걸렸다. 그 이유는 Daum 우편번호 API를 쉽게 사용할 수 있도록 구현해놓은 라이브러리를 추가하고, 해당 라이브러리가 제공하는 컴포넌트를 import 했을 때 _**'windows is not defined'**_ 라는 에러가 발생했기 때문이다.



```javascript
<template>
...
</template>

<script>
import {VueDaumPostcode} from "vue-daum-postcode"
...
</script>
```

![에러 발생](<.gitbook/assets/image (3).png>)



해당 문제를 해결하기 위해 많은 분석과 조사를 진행했다.&#x20;

먼저 라이브러리를 사용할 때 추가로 설정이 필요한 것이 있는지 확인하기 위해, 해당 라이브러리(`vue-daum-postcode`)의 **도큐먼트를 살펴봤다.**

{% embed url="https://www.npmjs.com/package/vue-daum-postcode" %}
Daum 우편번호 API Component 라이브러리
{% endembed %}



도큐먼트를 봤을 때는 잘못 설정한 부분이 보이지 않아, 해당 라이브러리를 다른 사람들은 어떻게 사용했는지 확인해보기 위해 **GitHub에 nuxt daum postcode를 검색**하여 코드를 분석해봤다.

{% embed url="https://github.com/yvid/AngelBoardProtoType" %}
vue-daum-postcode를 사용한 리포지토리 1
{% endembed %}

{% embed url="https://github.com/wf-eojinchoi/wafos" %}
vue-daum-postcode를 사용한 리포지토리 2
{% endembed %}



위의 리포지토리들을 살펴봤을 때는 거의 다른 점이 존재하지 않아, **nuxt에서 'window is not defined ' 에러가 발생하는 이유**에 대해 조사를 진행했다.

조사를 해본 결과, 아직 window가 존재하지 않는데 **import한 컴포넌트가 서버 사이드 렌더링 통해 컴포넌트를 바인딩하려하기 때문에** 문제가 발생했음을 알 수 있었다.

{% embed url="https://stackoverflow.com/questions/54979644/window-is-not-defined-in-nuxt-js" %}
구글에 'nuxt windows is not defined' 를 검색하면 나오는 첫 번째 게시물
{% endembed %}



그래서 **import문을 제거하고 vue-daum-postcode 태그만 작성**한 결과, 정상적으로 컴포넌트를 바인딩 시킬 수 있었다.

```javascript
<template>
  <el-dialog v-if="visible" id="addressDialog" title="주소 검색" 
      :visible.sync="visibleDialog" append-to-body
      model-append-to-body="false" :show-close="false" @close="closeDialog">
    <el-row style="margin-top: -20px">
      <el-col>
        <vue-daum-postcode v-if="visible" @complete="onComplete"/>
      </el-col>
    </el-row>
  </el-dialog>
</template>

...
```

![다이얼로그 형태로 연동한 Daum 우편번호 API](<.gitbook/assets/Screen Shot 2022-02-18 at 4.10.22 PM.png>)



위와 같이 API 연동은 잘 됐으나, 또 문제가 발생했다. 이는 처음에 API를 불러올 때는 정상적으로 불러오지만 **그 후로는 못 불러오는 문제가 발생**했기 때문이다.

![두 번째로 API를 호출했을 경우, API가 불러와지지 않는다.](<.gitbook/assets/image (1).png>)



위의 문제를 해결하기 위해 개발자 도구에서 Network를 확인해 본 결과, 처음 주소 검색 다이얼로그가 띄워졌을 때는 API를 호출하지만 그 다음부터는 API를 호출하지 않는 것을 알 수 있었다.&#x20;

호출하지 않는 이유에 대해 생각해본 결과, Vue가 기존에 Daum 우편번호 API를 rendering 했던 것에 대해서는 **re-rendering을 하지 않는 것**으로 판단했다. 기존에는 element-ui에서 제공하는 dialog의 속성인 visible.sync를 사용하여 다이얼로그의 가시성을 변경했지만, 문제를 해결하기 위해 **Vue가 re-rendering 할 수 있도록 v-if 를 추가하여 가시성을 변경**했다.

```javascript
// 기존 코드
<template>
  <el-dialog id="addressDialog" title="주소 검색" 
      :visible.sync="visibleDialog" append-to-body
      model-append-to-body="false" :show-close="false" @close="closeDialog">
    <el-row style="margin-top: -20px">
      <el-col>
        <vue-daum-postcode @complete="onComplete"/>
      </el-col>
    </el-row>
  </el-dialog>
</template>

// 수정한 코드 (v-if 추가)
<template>
  <el-dialog v-if="visible" id="addressDialog" title="주소 검색" 
        :visible.sync="visibleDialog" append-to-body
        model-append-to-body="false" :show-close="false" @close="closeDialog">
    <el-row style="margin-top: -20px">
      <el-col>
        <vue-daum-postcode @complete="onComplete"/>
      </el-col>
    </el-row>
  </el-dialog>
</template>
```



위의 과정들을 거쳐 Daum 우편번호 API 연동을 마치고, **Daum API를 통해 조회된 주소는 Kakao API로 위도와 경도를 조회**해오는 로직과 **백엔드로 사이트 정보를 등록**하는 로직까지 연동하여 사이트 등록 기능 구현을 완료했다.

## 개선 및 목표

* 예상치 못한 문제들이 많이 발생하여 생각보다 구현이 오래걸렸다. 다음부터는 **구현해야 하는 것을 세세하게 나눠 하루나 이틀 정도 여유를 둔 계획**을 세워야겠다.

