---
title:  "Spring Boot 개발환경 세팅(1)"
excerpt: "✨OpenJDK 17 설치 및 환경 설정✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot1/

toc: true

date: 2023-11-10
last_modified_at: 2023-11-10
---

## 🌿OpenJDK 17 설치


<div align="center">
    <img src="/assets/images/java.png" alt="java" width="50%" itemprop="image">
</div>

### 1. JDK 설치

오라클 홈페이지에서 다운로드 [OpenJDK17](https://www.oracle.com/java/technologies/downloads/#java17)

<img src="/assets/images/installer.png" alt="java" width="80%" itemprop="image">

나는 Window 환경이므로 Windows → x64 Installer를 다운로드 했다.

### 2. 환경 변수 설정

설정 → 시스템 → 정보 → 고급 시스템 설정 → 환경 변수 Click

<img src="/assets/images/java_home.png" alt="java" width="70%" itemprop="image">

- [새로 만들기]를 클릭하여 변수 이름을 [JAVA_HOME]로 하고 변수 값을 설치 경로로 넣어준다.<br/>

<img src="/assets/images/classpath.png" alt="java" width="70%" itemprop="image">


- 다시 새로 만들기를 클릭 후 변수 이름은 [CLASSPATH]로 하고 변수 값은 [%JAVA_HOME%\lib]를 넣어준다.<br/>


<img src="/assets/images/path.png" alt="java" width="60%" itemprop="image">

위에 두 가지를 완료했다면 Path를 선택 후 [편집]을 클릭하여 [새로 만들기]로 경로를 추가해 준다.

경로 [%JAVA_HOME%\lib]

완료 후 cmd 창에서 버전 확인을 진행해 주는 것이 좋다.

```cmd
java -version
```

<img src="/assets/images/cmd.png" alt="java" width="80%" itemprop="image">
