---
layout: single
title:  "[포트폴리오] 개발 실험실 프로젝트"
categories:
  - project
tag: ["AI Agent", "RAG", "Automation", "Chrome Extension", "Productivity", "Utility"]
last_modified_at : 2026/01/24
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
search: true
---

포트폴리오 Dev Lab 섹션입니다. 데이터는 포트폴리오 저장소의 JSON에 직접 정의되어 있습니다.

![포트폴리오 Dev Lab](/assets/images/portfolio/python_tableau.png)

### A2A Dashboard Generator
AI Agent를 활용하여 Google Sheets 요청 기반의 대시보드 이미지를 자동 생성하는 시스템입니다.
- 링크: [https://github.com/woolfie1101/a2a-dashboard](https://github.com/woolfie1101/a2a-dashboard)

### 📊 A2A Dashboard Generator
AI Agent들을 활용한 자동 대시보드 이미지 생성 시스템입니다.

### 🛠 주요 기능
- **스마트 요청 관리**: Google Sheets를 인터페이스로 활용하여 생성 요청을 관리합니다.
- **RAG 연동**: RAG(Retrieval Augmented Generation) 기술을 통해 데이터를 정교하게 분석합니다.
- **자동 이미지 생성**: 데이터 분석 결과를 바탕으로 이미지 생성 AI를 연동하여 전문적인 대시보드 이미지를 자동으로 제작합니다.

이 프로젝트는 복잡한 데이터 시각화 과정을 AI 자동화 워크플로우로 해결하는 것을 목표로 합니다. 상세 코드는 아래 깃허브 링크에서 확인하실 수 있습니다.

### NotebookLM Exporter
Google NotebookLM의 인사이트를 PDF, Markdown 등 다양한 형식으로 즉시 저장할 수 있는 크롬 확장 프로그램입니다.
- 링크: [https://chromewebstore.google.com/detail/notebooklm-exporter/nkdglgblompfaikoioecbimpbfincfea?hl=ko&utm_source=ext_sidebar](https://chromewebstore.google.com/detail/notebooklm-exporter/nkdglgblompfaikoioecbimpbfincfea?hl=ko&utm_source=ext_sidebar)

### 🛠️ 불편함에서 시작된 '난생처음'의 도전
Google NotebookLM을 애용하면서 생성된 결과물을 복사하거나 저장할 방법이 없어 큰 불편함을 느꼈습니다. 처음에는 제가 편하려고 만든 작은 기능이었지만, '다른 사람들도 분명 이 기능이 필요할 것'이라는 확신이 들어 크롬 웹스토어 배포에 도전하게 되었습니다.

### 🎢 4번의 거절, 그리고 값진 성공
호기롭게 시작했지만 배포 과정은 결코 쉽지 않았습니다. 구글의 보안 및 배포 가이드라인에 부딪혀 무려 **4번이나 거절(Reject)**당하는 시련이 있었습니다. 하지만 포기하지 않고 가이드를 분석해 코드를 수정했고, 마침내 승인을 받아냈을 때의 기쁨은 대단했습니다. '딱 10명만이라도 내 도구가 도움이 되었으면 좋겠다'는 마음으로 올렸는데, 어느덧 **315명**이 넘는 유저분들이 사용해주시는 작지만 강력한 도구가 되었습니다.

### 🌟 구글은 없지만 'Exporter'에는 있는 것
공교롭게도 배포 이후 얼마 지나지 않아 NotebookLM 자체에 복사 기능이 생겼습니다. 하지만 여기서 멈추지 않고, 공식 기능보다 훨씬 강력하고 정교한 'Exporter'만의 가치를 더했습니다.

- **📄 진짜 내 파일로 소유하기**: 단순히 복사하는 것을 넘어 PDF, TXT, Markdown(MD) 형식으로 완벽하게 다운로드합니다.
- **✨ 독보적인 가독성**: 본문의 불필요한 인용 번호를 자동으로 제거해 읽기 편한 상태로 정리해줍니다.
- **🇰🇷 깨지지 않는 한글**: PDF 변환 시 발생하는 고질적인 한글 폰트 깨짐 현상을 완벽하게 해결했습니다.
- **✂️ 지능적인 페이지 분할**: 문장이 중간에 잘리지 않도록 똑똑하게 페이지를 나누어 저장합니다.

사용자분들의 피드백을 통해 기능이 개선될 때마다, 기술이 실질적인 도움을 줄 수 있다는 사실에 큰 보람을 느낍니다.

상세 코드와 실행 로직은 깃허브([Repository](https://github.com/woolfie1101/notebooklm-exporter))에서 확인하실 수 있습니다.
