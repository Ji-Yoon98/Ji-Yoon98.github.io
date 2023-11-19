---
title:  "Spring Boot Thymeleaf layout"
excerpt: "✨Thymeleaf layout으로 화면 구성하기✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot6/

toc: true

date: 2023-11-19
last_modified_at: 2023-11-19
---

## 🌿 Thymeleaf layout 설정

### 1. build.gradle에 dependency 추가

```bash
implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

### 2. 공통 양식 페이지 제작

<img src="/assets/images/layout-list.png" alt="securityAuto" width="50%" itemprop="image">

#### header.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <th:block th:fragment="headerFragment">
     <h1>header</h1>
  </th:block>
</html>
```

#### footer.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <th:block th:fragment="footerFragment">
     <h1>footer</h1>
  </th:block>
</html>
```

#### default_layout.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
  <head>
  	<meta charset="UTF-8">
  	<title>title</title>
  </head>
  <body>
    <th:block th:replace="fragments/header :: headerFragment"></th:block>
    <th:block layout:fragment="content"></th:block>
    <th:block th:replace="fragments/footer :: footerFragment"></th:block>
  </body>
</html>
```

#### templates/content/home.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/default_layout}">

<th:block layout:fragment="content">
  본문
</th:block>
</html>
```

### 3. Controller 연동

#### HomeController
```java
package com.team.mztelecom.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
	
	@GetMapping(value = "/")
	public String home() {
		return "content/home";
	}
```
