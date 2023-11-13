---
title:  "Spring Boot 생성"
excerpt: "✨Initializr✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot3/

toc: true

date: 2023-11-13
last_modified_at: 2023-11-13
---

## 🌿 Spring boot 프로젝트 생성

initializr으로 프로젝트 생성<br/>[바로가기](https://start.spring.io/)

*스프링 부트 3.0부터는 Java17 이상의 버전을 사용
<img src="initializr.png">

✅ Project - 빌드배포 도구 선택

✅ Language - 사용 언어 선택

✅ SpringBoot - 스프링부트 버전 선택

✅ Project Metadata

- Group : 기업도메인명 
- Artifact : 빌드되어 나올 결과물
- Name : 프로젝트
- Description : 설명
- Package name : Group과 Artifact 작성 시, 자동으로 생성

✅ Dependencies - 라이브러리 세팅

ENERATE로 프로젝트 다운로드 후 workspace에 압축을 푼 파일을 넣어준 후 STS에서 import 해주면 끝!!
