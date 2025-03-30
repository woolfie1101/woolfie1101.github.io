---
layout: single
title:  "[TABLEAU] 카카오톡 UX를 통해 배운 접근성의 중요성"
categories: 
  - tableau, 리눅스  #카테고리
tag: [tableau, 태블로, 리눅스, tableau server] #태그
toc: true  #오른쪽에 있는 목차
toc_sticky: true #목차 고정
author_profile: true  #왼쪽에 자기 소개란 프로필을 이 페이지에 들어갈때 끄는 기능
sidebar:
    nav: "sidebar-category" #navigation.yml에 있는 docs를 뜻한다.
search: true  #이 페이지는 검색에 나옴.
---

# [문제 해결] Tableau Server에서 태국어가 깨질 때, 리눅스 폰트 설치로 해결한 사례

필자가 다니는 회사는 데이터를 가장 많이 다루는 조직 중 하나로,  
Tableau를 주력 분석 도구로 사용하고 있다.  
개발 업무 외에도 가끔은 Tableau Server와 관련된 이슈도 직접 해결해야 하는 경우가 있다.

이번 게시글은 Tableau 이슈처럼 보이지만,  
사실상 **리눅스 서버 환경에서의 폰트 문제 해결 사례**이기 때문에 정리해서 공유한다.

---

## 🧩 문제 상황

Windows 환경에서 Tableau Desktop으로 개발할 때는 **태국어 텍스트가 정상적으로 표시**되었으나,  

Tableau Server(Ubuntu 리눅스)에 퍼블리시한 후에는 **태국어가 깨져서 보이는 현상**이 발생했다.

예시:

- ✅ Tableau Desktop (Windows): `สวัสดี` (정상)
- ❌ Tableau Server 접속 시: `���`, `▯▯▯` (깨짐)
  

![image.png](/assets/images/2025/03/30/tailand1.png)
---

## 🔍 원인 분석

Tableau Server는 리눅스 환경에서 실행되며,  
서버가 **텍스트를 이미지로 렌더링해서 클라이언트(브라우저)로 전달**하는 방식으로 작동한다.

이 과정에서 리눅스 서버에 해당 언어의 폰트가 설치되어 있지 않으면,  
**해당 언어의 텍스트가 깨져서 보일 수 있다.**

즉, 이 문제는 Tableau의 기능 문제가 아닌, **리눅스 폰트 환경의 문제**였다.

---

## 🛠 해결 방법

### 1. 태국어 폰트 설치

Ubuntu 리눅스 서버에 Google에서 제공하는 **Noto Sans Thai** 및  
TLWG(Thai Linux Working Group)의 태국어 전용 폰트를 설치했다.

```bash
sudo apt update
sudo apt install fonts-noto-core fonts-noto-cjk \
                 fonts-tlwg-garuda fonts-tlwg-loma fonts-tlwg-umpush
```

### 2. 폰트 캐시 갱신

설치한 폰트를 시스템이 인식할 수 있도록 캐시를 갱신했다.

```bash
fc-cache -fv
```

캐시가 성공적으로 갱신되면, 마지막에 fc-cache: succeeded라는 메시지가 출력된다.

### 3. 폰트 설치 확인

다음 명령어를 통해 태국어 폰트가 시스템에 등록되었는지 확인했다.

```bash
fc-list | grep -i thai
```
출력 예시 (정상적으로 설치된 경우):

```bash
/usr/share/fonts/truetype/noto/NotoSansThai-Regular.ttf: Noto Sans Thai:style=Regular
/usr/share/fonts/truetype/noto/NotoSansThai-Bold.ttf: Noto Sans Thai:style=Bold
/usr/share/fonts/truetype/noto/NotoSerifThai-Regular.ttf: Noto Serif Thai:style=Regular
/usr/share/fonts/truetype/noto/NotoSerifThai-Bold.ttf: Noto Serif Thai:style=Bold
```

### 4. Tableau Server 재시작

폰트는 설치되었지만, Tableau Server가 이를 인식하려면 서버를 재시작해야 한다.

```bash
tsm restart
```
⚠️ tsm restart는 리눅스 전체를 재시작하지 않고, Tableau Server 프로세스만 재시작한다.


## 결과

Tableau Server 재시작 이후, 깨져 보이던 태국어가 정상적으로 표시되기 시작했다.

![image.png](/assets/images/2025/03/30/tailand2.png)

## 결론

- Tableau는 서버에서 텍스트를 이미지로 렌더링하기 때문에, 리눅스 서버에 해당 언어의 폰트가 설치되어 있어야 한다.

- 태국어 폰트(Noto Sans Thai, TLWG 폰트) 설치 후 캐시를 갱신하고, Tableau Server만 재시작하면 문제는 말끔히 해결된다.

- 단순히 Tableau 문제로 보일 수 있지만, 리눅스 시스템 환경의 기본 설정도 고려해야 함을 보여주는 사례였다.



