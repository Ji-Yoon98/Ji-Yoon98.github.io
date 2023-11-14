---
title:  "Spring Boot git에서 Gradle파일 import시 에러"
excerpt: "✨The import org.springframework cannot be resolved✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot4/

toc: true

date: 2023-11-14
last_modified_at: 2023-11-14
---

## 🌿 "The import org.springframework cannot be resolved" 에러 발생

gitlab에 있는 gradle프로젝트를 import하자 바로 에러가 남.

**원인** : 프로젝트 import시 gradle이 안 가져와져서 뜨는 문제

**해결** : 프로젝트 우클릭 → configure → add gradle nature 실행

간단하게 해결 완료!!

