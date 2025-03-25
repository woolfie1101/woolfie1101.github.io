---
layout: single
title:  "AWS 인스턴스 쉽게 접근하기 (SSH Host Aliasing)"
date: 2026-03-17
categories:
  - blog
  - aws
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["AWS"]
search: true
---

# AWS 인스턴스 쉽게 접근하기 (SSH Host Aliasing)

원문: [https://www.notion.so/326bf506e994806b94c2e66a57a789d7](https://www.notion.so/326bf506e994806b94c2e66a57a789d7)

# [Windows 유저]

1. PowerShell 열기

1. PowerShell에 `cd ~/.ssh` 명령어 입력 후 엔터 

1. PowerShell 에 `notepad config` 명령어 입력 후 엔터

1. `예` 버튼 클릭

1. aws에 들어가서 퍼블릭 DNS 를 복사아이콘을 눌러서 복사

1. 아래 내용을 복사하여 메모장 화면에 넣고 저장
무조건 C드라이브에 .pem 파일 저장해주시고, 경로에는 “” 큰따옴표 붙여주세요!!!

1. PowerShell에 아래 명령어를 입력하고 엔터

1. 6번에 설정한 명령어에서 `ssh`를 앞에 추가하여 터미널에 입력 후 엔터를 하면 aws 에 쉽게 들어갈 수 있다.

# [Mac 유저]

1. 맥 터미널 열기

1. 터미널에 `nano ~/.ssh/config` 명령어 입력 후 엔터

1. 그러면 아래와 같은 빈 화면이 뜬다.

1. aws에 들어가서 퍼블릭 DNS 를 복사아이콘을 눌러서 복사

1. 아래 내용을 복사하여 빈 화면에 넣는다.

1. 그리고 아래 단축키를 눌러 저장 후 종료한다.

1. 5번에 설정한 명령어에서 `ssh`를 앞에 추가하여 터미널에 입력 후 엔터를 하면 aws 에 쉽게 들어갈 수 있다.
