---
title:  "Spring Boot 개발환경 세팅(2)"
excerpt: "✨STS4 설치 및 환경설정✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot2/

toc: true

date: 2023-11-13
last_modified_at: 2023-11-13
---

## 🌿 STS4 설치


스프링 공식 홈페이지에서 다운로드<br/> [바로가기](https://spring.io/tools)

<img src="/assets/images/sts4.png" alt="sts4" width="80%" itemprop="image">

'Spring Tools 4 for Eclipse'를 다운로드

## 🌿 STS4 환경설정

### 1. JVM 지정하기

STS.ini 파일에서 STS가 사용할 JVM을 지정해 준다.

<img src="/assets/images/stsini.png" alt="sts4" width="80%" itemprop="image">

<img src="/assets/images/stsiniset.png" alt="sts4" width="80%" itemprop="image">

javaw.exe 경로 지정

### 2. 기본 인코딩 설정

메뉴 [Windows] → [Preferences] 클릭

- [General] → [Content Types] → [Text] 에서 Default encoding 란에 UTF-8을 입력
- [General] → [Editors] → [Text Editors] > [Spelling] 에서 UTF-8을 선택
- [General] → [Workspace] 에서 Text file encoding → UTF-8
- [enc]검색 → CSS, HTML, JSP, XML → UTF-8

### 3. JavaEE 플러그인 설치(필요한 경우)

JSP or JavaScript 파일을 생성 등의 JavaEE 기능을 사용하려면 별도로 설치를 해줘야함.

<img src="/assets/images/javaEE.png" alt="sts4" width="80%" itemprop="image">

- [Help] → [Eclipse Marketplace] → eclipse web 검색 → [Eclipse Enterprise Java and Web Developer Tools] 다운로드

- [Window] → [Preferences] → [General] → [Editors] → [File Associations] <br/>
File types에 *.js를 추가 Associated editors에서 Generic Text Editor를 'Default'로 지정
