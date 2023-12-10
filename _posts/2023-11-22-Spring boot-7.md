---
title:  "Spring Boot Security"
excerpt: "✨Security 설정하기✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot7/

toc: true

date: 2023-11-22
last_modified_at: 2023-11-22
---

## 🌿 Security

> 스프링 시큐리티(Spring Security)란 스프링 기반의 어플리케이션의 보안(인증과 권한)을 담당하는 프레임워크이다.<br/>
> 보안과 관련해서 체계적으로 많은 옵션들을 지원해 주며, 필터(Filter) 기반으로 동작하기 때문에 스프링 MVC 와 분리되어 관리 및 동작한다.

- 인증(Authentication) : 해당 사용자가 본인이 맞는지 확인하는 과정
- 인가(Authorization) : 해당 사용자가 요청하는 자원을 실행할 수 있는 권한이 있는가를 확인하는 과정


## 🌿 시큐리티 설정하기

### 1. build.gradle에 dependency 추가

```bash
implementation 'org.springframework.boot:spring-boot-starter-security'
implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6'
testImplementation 'org.springframework.security:spring-security-test'
```

security6사용을 위해선 Spring boot3.x 이상 사용해야함


### 2. SecurityConfig파일 생성

<img src="/assets/images/config.png" alt="securityAuto" width="50%" itemprop="image">

#### SecurityConfig.java

```java
package com.team.mztelecom.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.annotation.Order;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity // 시큐리티 관리 설정
public class SecurityConfig {

	@Bean // 빈으로 등록해주면 자동으로 필터에 시큐리티 설정을 커스텀으로 진행 할 수 있음
	protected SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		
		http
		 	// 특정한 경로 허용, 거부 설정 (작성은 람다식)
			.authorizeHttpRequests((auth) -> auth 
					// permitAll() -> 를 사용하면 권한이 없는 모든 사람들이 들어갈 수 있음.
					.requestMatchers("/", "/**.do", "/js/**", "/css/**","/images/**", "/login").permitAll()
					
					// ADMIN이라는 권한을 가진 사람만 접속 가능
					.requestMatchers("/admin").hasRole("ADMIN")
					
					// ADMIN, USER 권한을 가진 사람만 접속 가능
					.requestMatchers("/myPage/**", "/purRevWrite").hasAnyRole("ADMIN","USER")
					.anyRequest().authenticated()
		    )
			// 권한이 없는 사람이 접속하려고하면 로그인 페이지로 자동연결 되는데
			// login 페이지가 없으면 시큐리티 자체 로그인 페이지로 넘어감
			.formLogin(formLogin -> formLogin
					.loginPage("/login") 
			)
			.logout((logout) -> logout.logoutUrl("/logout"))
			
			// 소셜 로그인 
			.oauth2Login((oauth2) -> oauth2.loginPage("/login"));

        return http.build();

	}
	
	// 패스워드 암호화 시켜주는 거
	@Bean
	BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
	
}

```

- @EnableWebSecurity: Spring Security를 활성화하여 웹 보안 설정을 관리하는 어노테이션
- SecurityFilterChain filterChain(HttpSecurity http): 보안 필터를 커스터마이징하기 위해 빈으로 등록된 메서드, HTTP 보안 설정을 정의
authorizeHttpRequests(): 특정 URL 경로에 대한 접근 권한을 설정하는 메서드, permitAll()을 통해 무조건 접근을 허용하거나 hasRole() 및 hasAnyRole()을 이용하여 특정 권한을 가진 사용자만 접근을 허용할 수 있다.
- formLogin(): 로그인 페이지를 지정하는 메서드, 사용자가 로그인하지 않은 상태에서 보호된 페이지에 접근하려고 할 때 자동으로 로그인 페이지로 이동시킴
- logout(): 로그아웃을 처리하기 위한 메서드, 로그아웃 URL을 설정하여 사용자가 로그아웃할 수 있도록 함.
- oauth2Login(): OAuth2를 이용한 소셜 로그인 설정을 담당하는 메서드, 소셜 로그인 페이지를 지정
- BCryptPasswordEncoder passwordEncoder(): 패스워드를 BCrypt 해싱하여 암호화하는 데 사용되는 빈을 등록

이렇게 초기 세팅을 했는데, 

```java
.requestMatchers("/", "/**.do", "/js/**", "/css/**","/images/**", "/login").permitAll()
```

이 부분 ``` "/**.do" ```은 권한이 걸려서 권한없이 접근이 가능한 페이지가 접근이 안돼는 이슈가 발생해서 넣게 되었다. <br/>
근데 생각해보니 프로그래밍은 위에서 부터 아래로 실행 됨.

수정된 코드 👇🏻

```java
.authorizeHttpRequests((auth) -> auth 
	.requestMatchers("/admin").hasRole("ADMIN")
	.requestMatchers("/myPage/**", "/purRevWrite").hasAnyRole("ADMIN","USER")
	.requestMatchers("/**" , "/js/**", "/css/**","/images/**", "/login").permitAll()
	.anyRequest().authenticated()
)
```

- 권한을 한번에 써줘도 괜찮지만 컨트롤러에서도 설정이 가능함

```java
@GetMapping(value = "/login")
@PreAuthorize("isAnonymous()")
public String login(Locale locale, Model model) {
	return "content/login";
}

@GetMapping(value = "/cart")
@PreAuthorize("isAuthenticated()")
public String cart(Locale locale, Model model) {
	return "content/cart";
}
```
