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

// import 생략

@EnableWebSecurity
@Configuration
public class SecurityConfig {
	
	@Bean
    protected SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		
		http
			.authorizeHttpRequests((auth) -> auth
					.requestMatchers("/**.do", "/js/**", "/css/**","/images/**", "/login").permitAll()
					.requestMatchers("/admin").hasRole("ADMIN")
					.requestMatchers("/myPage/**", "/purRevBoard/purRevWrite").hasAnyRole("ADMIN","USER")
					.anyRequest().authenticated()
		    )
			.formLogin(formLogin -> formLogin
					.loginPage("/login")
			)
			.logout((logout) -> logout.logoutUrl("/logout"));

        return http.build();

	}
	
	@Bean
	PasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
	
}
```

~~~ ing 
