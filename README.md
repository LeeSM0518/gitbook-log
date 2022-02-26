---
description: 일일 회고 23회차
cover: .gitbook/assets/Frame 77.png
coverY: 165.79833206974982
---

# 🙂 2022.02.25

## 할일 및 한일

* [x] `배터리 관제 시스템` 사이트 관리 페이지 구현 (90%)
* [x] `배터리 관제 시스템` 프론트엔드 토큰 전달 및 재발행 로직 구현

## 경험 및 배움

### `배터리 관제 시스템` 사이트 관리 페이지 구현

오늘은 사이트 관리 페이지의 사이트 수정 및 검색 기능을 구현했다.&#x20;

사이트 수정은 아래와 같이 기존 정보를 가져온 후, 정보를 수정할 수 있도록 구현했다. 사용자가 수정 버튼 클릭 시, 사이트 식별자를 통해 정보를 가져오는데 기존 사이트 상세 조회 API에는 사이트 담당자 식별자와 위도, 경도 정보를 응답하지 않았다. 그래서 백엔드를 개발하고 있는 팀원에게 API 수정을 요청한 상태이다.

![사이트 수정 기능](<.gitbook/assets/image (5).png>)

현재는 사이트 담당자 정보가 없으므로 사이트 담당자를 변경하는 기능이 없지만, 추후에 API가 수정되면 넣을 예정이다.



사이트 검색 기능은 아래와 같이 사이트 이름과 주소로 검색이 가능하도록 구현했다.

![사이트 검색 기능 (이름으로 검색)](<.gitbook/assets/image (3).png>)

![사이트 검색 기능 (주소로 검색)](<.gitbook/assets/image (2).png>)

해당 기능을 구현하는데 페이징과 렌더링 관련 문제가 발생했었다.

기존에는 검색을 고려하지 않고 사이트 테이블에 페이징을 적용했었으나, 검색 기능을 구현하고 10개 이상의 사이트와 일치하는 키워드를 검색하면 페이지에서 밀려나가는 문제가 발생했었다. 그래서 다음 코드와 같이 `search` 라는 검색어가 입력되어 필터링된 요소들을 기준으로 페이징을 다시 적용하는 로직으로 변경했다.

```javascript
...
computed: {
  filter() {
    if (this.search === '' && this.pagedSite) {
      return this.pagedSite[this.currentPage - 1]
    } else {
      const sites = this.sites.filter((data) => (data.name.toLowerCase() + data.address.toLowerCase()).includes(this.search.toLowerCase()));
      const chunk = 10;
      const totalPage = sites.length / chunk + (sites.length % chunk === 0 ? 0 : 1)
      const pages = []
      for (let page = 0; page < totalPage; page++) {
        const start = chunk * page;
        pages.push(sites.slice(start, start + chunk))
      }
      return pages[this.currentPage - 1]
    }
  }
},
...
```

* 새로 사이트를 추가하거나 삭제했을 경우 re-rendering을 해야하는데 `pagedSite` 가 변경되었음을 `computed` 가 판단하지 못 해서, if문에서 `pagedSite` 가 존재하는지 확인하도록 구현했다.



### `배터리 관제 시스템` 프론트엔드 토큰 전달 및 재발행 로직 구현

기존 프론트엔드는 AccessToken과 RefreshToken을 쿠키로만 관리하고, API를 요청할 때 헤더 정보로 AccessToken을 전달하는 로직은 미구현 상태였다. 이처럼 구현을 해놔도 백엔드에 인증/인가 로직이 부분적으로 구현되어 있으므로 다른 기능들을 구현하고 테스트 할 수 있었다.&#x20;

이 와중에 사이트 관리 페이지를 구현하다보니 회원 상세 정보 조회 API 요청이 필요해졌으나, 회원 상세 정보 조회 API를 요청하면 관리자 권한을 갖고 있거나 자신과 일치하는 회원만 조회할 수 있도록 구현되어 있기 때문에 요청이 실패하는 문제가 발생했다. 그래서 **프론트에서 API 요청할 때 헤더에 AccessToken을 담아서 요청하는 로직 구현을 진행했다.**



