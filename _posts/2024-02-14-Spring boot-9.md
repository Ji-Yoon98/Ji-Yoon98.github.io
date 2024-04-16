---
title:  "Spring Boot Security 소셜 로그인 [카카오, 구글]"
excerpt: "✨Security 소셜 로그인 구현✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot9/

toc: true

date: 2024-02-14
last_modified_at: 2024-02-14
---

## 🌿 Security 소셜 로그인

<img src="/assets/images/spring_security.png" alt="spring_security" width="100%" itemprop="image">

### 1. 소셜 애플리케이션 등록

[참고](https://deeplify.dev/back-end/spring/oauth2-social-login)

먼저 사용하려는 소셜 플랫폼(예: Google, Facebook, GitHub 등)의 개발자 포털에 들어가서 애플리케이션을 등록. </br>
등록 과정에서는 클라이언트 ID 및 시크릿 키와 같은 인증 정보를 발급받는다. 

나의 경우 **(Google, kakao)**


### 2. 의존성 추가

```bash
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'
```

### 3. application-oauth.properties 파일 생성 및 기존 properties에 내용 추가

**application.properties** 파일에 아래 내용 추가

```
# spring security OAuth
spring.profiles.include=oauth
```

**application-oauth.properties**엔 Goole, kakao 관련 값들을 넣어줌.

```
# Google
spring.security.oauth2.client.registration.google.client-id: 구글 클라이언트 ID
spring.security.oauth2.client.registration.google.client-secret: 구글 클라이언트 시크릿 키
spring.security.oauth2.client.registration.google.scope: 요청할 권한 범위(프로필 및 이메일)
spring.security.oauth2.client.registration.google.redirect-uri: 사용자 인증 후 리디렉션될 URI

# kakao
spring.security.oauth2.client.registration.kakao.client-id: 카카오 클라이언트 ID
spring.security.oauth2.client.registration.kakao.client-secret: 카카오 클라이언트 시크릿 키
spring.security.oauth2.client.registration.kakao.scope: 요청할 권한 범위
spring.security.oauth2.client.registration.kakao.redirect-uri: 사용자 인증 후 리디렉션될 URI
spring.security.oauth2.client.provider.kakao.authorization-uri: 카카오 OAuth2 인증 엔드포인트 URI
spring.security.oauth2.client.provider.kakao.token-uri: 카카오 OAuth2 토큰 엔드포인트 URI
spring.security.oauth2.client.provider.kakao.user-info-uri: 사용자 정보 엔드포인트 URI
spring.security.oauth2.client.provider.kakao.user-name-attribute: 사용자 이름 속성명
```

### 4. SecurityConfig 파일에 oauth2 관련 설정

```java
// 소셜 로그인 
.oauth2Login((oauth2) -> oauth2.loginPage("/login"));
```

### 5. 로그인 페이지 소셜 로그인 경로 설정

**login.html**

```html
<div class="kakao-login">
  <a th:href="@{/oauth2/authorization/kakao}">
    <img th:src="@{/images/img_kakao.png}" alt="kakao">
    <br/>
    KAKAO 
  </a>
</div>
			        
<div class="google-login">
  <a th:href="@{/oauth2/authorization/google}">
    <img th:src="@{/images/img_google.png}" alt="google">
    <br/>
    Google 
  </a>
</div>
```

### 6. Service

소셜 로그인을 통해 사용자가 처음으로 로그인하는 경우를 처리하는 메서드 생성

```java
@Transactional
public CustBas whenSocialLogin(String providerTypeCode, String custId, String custNm, String custEmail) {
	Optional<CustBas> opCustBas = findByCustId(custId);
	
	// 존재하는 고객인 경우, 해당 고객 정보 반환
	if (opCustBas.isPresent()) { return opCustBas.get(); }
	
	// 새로운 고객 등록을 위해 고객 정보 설정
	CustBasDTO request = new CustBasDTO();
	request.setCustId(custId);
	// 소셜 로그인를 통한 가입시 비번 X
	request.setCustPassword("");
	request.setCustNm(custNm);
	request.setCustEmail(custEmail);
	request.setCustNo("Unknown");
    
	String uniqueIdfyNo = UUID.randomUUID().toString();
	request.setCustIdfyNo(uniqueIdfyNo);
       
	return save(request);
}
```

### 7. CustomOAuth2UserService 생성

```java
@Service
@Transactional
@RequiredArgsConstructor
public class CustomOAuth2UserService extends DefaultOAuth2UserService {
	
	private static final Logger logger = LoggerFactory.getLogger(CustService.class);
	
	private final CustService custService;
	
	 @Override
	 @Transactional
	 public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
		 OAuth2User oAuth2User = super.loadUser(userRequest);
		 Map<String, Object> attributes = oAuth2User.getAttributes();
		 
		 logger.debug("소셜로그인 service");
		 logger.debug("Attributes: " + attributes.toString());
		 
		 // kakao
		 Map attributesProperties = (Map) attributes.get("properties");
		 Map attributesAccount = (Map) attributes.get("kakao_account");
		 
		 String custNm = "Unknown";
		 String custEmail = "Unknown";
		 
		 if(attributesProperties != null) {
			 custNm = (String) attributesProperties.get("nickname");
		 } else if(attributes != null) {
			 custNm = (String) attributes.get("name");
		 }
		 
		 if(attributesAccount != null) {
			 custEmail = (String) attributesAccount.get("email");
		 } else if(attributes != null) {
			 custEmail = (String) attributes.get("email");
		 }

     		 // OAuth2를 통해 인증된 사용자의 고유 식별자
		 String oauthId = oAuth2User.getName();

		 // OAuth2 클라이언트의 등록 ID
		 String providerTypeCode = userRequest.getClientRegistration().getRegistrationId().toUpperCase();

     		 //  소셜 로그인을 통해 생성된 ID
		 String custId = providerTypeCode + "_%s".formatted(oauthId);

		 // whenSocialLogin 메서드를 호출
		 CustBas custBas = custService.whenSocialLogin(providerTypeCode, custId, custNm, custEmail);
		 
		 return new CustomOAuth2User(custBas.getCustId(), custBas.getPassword(), custBas.getAuthorities());
	 }
}

// User 클래스를 확장하고 OAuth2User 인터페이스를 구현
class CustomOAuth2User extends User implements OAuth2User {

    // CustomOAuth2User의 생성자로, 사용자 이름, 비밀번호 및 권한을 받아 초기화
    public CustomOAuth2User(String username, String password, Collection<? extends GrantedAuthority> authorities) {
        super(username, password, authorities);
    }

    // 사용자의 추가 속성을 다루지 않음
    @Override
    public Map<String, Object> getAttributes() {
        return null;
    }

    // 사용자 이름 반환
    @Override
    public String getName() {
        return getUsername();
    }
}
```

- @RequiredArgsConstructor: 필드를 기반으로 생성자를 자동으로 생성
- loadUser: 스프링 시큐리티에서 제공하는 메서드로, OAuth2 로그인 시 사용자 정보를 로드함.
- userRequest: OAuth2 로그인 요청에 대한 정보.
- oAuth2User: 부모 클래스인 DefaultOAuth2UserService를 통해 사용자 정보를 가져옴.
- attributesProperties, attributesAccount: 소셜 플랫폼에 따라 다른 속성을 가진 사용자 정보를 담고 있음.

