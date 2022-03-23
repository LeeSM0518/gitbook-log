---
description: 일일 회고 11회차
cover: .gitbook/assets/Frame 85.png
coverY: 199
---

# 😄 2022.02.13

## 할일 및 한일

* [x] `중고 경매 애플리케이션` API 구현

## 경험 및 배움

### `중고 경매 애플리케이션` API 구현

오늘은 중고 경매 애플리케이션의 회원가입 로직을 구현했다. 해당 로직은 다음과 같다.

1. _사용자는 카카오 로그인을 진행한다._
2. _사용자가 카카오 로그인을 성공할 시, `OAuth 식별자`와 `이메일`, `전화번호`, `닉네임`, `관심 카테고리 리스트`를 애플리케이션에 전송하며 회원가입을 요청한다._
3. _애플리케이션은 사용자로부터 전송된 데이터의 유효성을 검증한다._
4. _유효성 검사를 마치고, OAuth 식별자와 이메일, 전화번호, 이름이 이미 존재하는지와 관심 카테고리 리스트의 카테고리들이 실제로 존재하는지 검증한다._
5. _새로운 사용자를 저장한다._



위의 로직에서 사용자가 전송한 데이터에 대해 유효성을 검사는 `jakarta bean validation` 라이브러리를 활용하여 구현했으며, 전화번호와 닉네임은 정해진 규칙이 있기 때문에 정규식으로 유효성 검사를 진행했다.



사용자의 데이터를 검증하고 저장하는 로직은 다음과 같이 구현했다.

```java
if (memberRepository.existsByEmail(request.getEmail())) {
  throw new EmailDuplicateException();
}
if (memberRepository.existsByPhoneNumber(request.getPhoneNumber())) {
  throw new PhoneNumberDuplicateException();
}
if (memberRepository.existsByOauthId(request.getOauthId())) {
  throw new OauthIdDuplicateException();
}
if (memberRepository.existsByName(request.getName())) {
  throw new NameDuplicateException();
}
if (!categoryRepository.existsByCategoryList(request.getInterests())) {
  throw new CategoryNotExistsException();
}
final Member member = memberRepository.save(Member.create(request));
```



위의 로직은 **데이터베이스에 5번 접근하게 되어** 회원가입 요청이 많이 발생했을 때 데이터베이스에 부하가 예상되어, 회원 테이블에 대해 한 번만 접근할 수 있는 방법이 없을까 하는 의문이 들어 다음과 같은 로직을 구현했었다.

```java
@Query(value =
      "SELECT EXISTS " +
          "(SELECT 1 " +
          "FROM members " +
          "WHERE email = :#{#request.email} OR " +
          "phone_number = :#{#request.phoneNumber} OR " +
          "oauth_id = :#{#request.oauthId} OR " +
          "name = :#{#request.name})",
      nativeQuery = true)
  boolean existsBySignUpRequest(@Param("request") SignUpRequest request);
```



위와 같이 구현을 한다면 데이터베이스에 4번 접근하는 것을 1번만 접근해도 되지만, 어떤 예외가 발생했는지 구분할 수 없게 된다. 회원가입은 새로운 사용자가 한 번만 요청하는 것이기 때문에 **처리 성능보다는 정확한 예외 응답이 더 필요할꺼 같다고 판단되어 기존 로직으로 구현하기로 결정했다.**



이처럼 구현을 마치고 **데이터 유효성 검사 성공 및 실패 테스트와 비즈니스 로직 성공 및 실패 테스트 코드를 작성**하여 회원가입 로직을 검증했다.

## 개선 및 목표

* 현재는 API 구현에 대한 템플릿을 구성해나가는 단계이므로 차근차근 구현하도록 하자.

