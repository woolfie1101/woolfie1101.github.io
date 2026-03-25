---
layout: single
title:  "mac OS tomcat 다운로드 및 설정 & intellij spring tomcat 연동"
date: 2023-10-08
categories:
  - blog
  - spring
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["기타", "백엔드(Spring)"]
search: true
---

# mac OS tomcat 다운로드 및 설정 & intellij spring tomcat 연동

원문: [https://www.notion.so/c8ee67c9842f42bb88db188cbe0233b4?pvs=1](https://www.notion.so/c8ee67c9842f42bb88db188cbe0233b4?pvs=1)

# 1. mac OS tomcat 다운로드 및 설정

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-0-31d484ad6680.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-1-3b5d6fe7c471.png)

찾기 쉽게 압축을 푼 뒤 폴더를 응용프로그램에 넣음.

bin 폴더까지 들어가서

cmd + opt + c 눌러서 경로 복사

```bash
$ cd /Applications/apache-tomcat-8.5.93/bin
$ ./startup.sh
```

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-2-3ad2f93ad1de.jpg)

[http://localhost:8080/](http://localhost:8080/) 을 아무 인터넷 브라우저에 들어가면 아래와 같이 뜬다. 그럼 성공

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-3-5a86f45ba508.jpg)

# 2. intellij spring tomcat 연동

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-4-ef121b7f32ed.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-5-1ad46a6e4f90.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-6-c6680e82cb3c.png)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-7-383f5899b9df.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-8-056cb5971d83.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-9-7ad98e1f2372.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-10-a9de8d50ef9a.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-11-966989e21305.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-12-06b6dda613c2.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-13-4b75d0c11165.jpg)

# 3. 외부 dependency 연결

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-14-31ba0592c2e7.png)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-15-29092dcffdd5.jpg)

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-16-fdef98774d03.jpg)

╰─ cd /Users/kim_joohee/Documents/insight_works_practice/src/main/webapp/WEB-INF/lib

![image](/assets/images/notion/2023/10/08/image-2023-10-08-mac-os-tomcat-다운로드-및-설정-intellij-spring-tomcat-연동.md-17-597c0f9dfcd8.jpg)
