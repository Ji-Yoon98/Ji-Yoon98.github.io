---
title:  "Spring Boot Security 기능 꺼두기"
excerpt: "✨인증단계 개발 미완료 시 Security✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot5/

toc: true

date: 2023-11-15
last_modified_at: 2023-11-15
---

## 🌿 Spring Security 기능 꺼두기 

서버 start시 Security 때문에 바로 로그인이 뜸, 인증단계를 개발하지 않았기에 시큐리티를 꺼둬야한다.

**해결** :  Application에서 exclude를 이용해 Security 기능을 끔.

<img src="/assets/images/securityAuto.png" alt="securityAuto" width="100%" itemprop="image">
