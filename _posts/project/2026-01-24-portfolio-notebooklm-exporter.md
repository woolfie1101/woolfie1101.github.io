---
layout: single
title:  "[포트폴리오] NotebookLM Exporter"
categories:
  - project
tag: ["Chrome Extension", "Productivity", "Utility"]
last_modified_at : 2026/01/24
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
search: true
---

![NotebookLM Exporter](/assets/images/portfolio/notebooklm_exporter.png)

Google NotebookLM의 인사이트를 PDF, Markdown 등 다양한 형식으로 즉시 저장할 수 있는 크롬 확장 프로그램입니다.
- 링크: [https://chromewebstore.google.com/detail/notebooklm-exporter/nkdglgblompfaikoioecbimpbfincfea?hl=ko&utm_source=ext_sidebar](https://chromewebstore.google.com/detail/notebooklm-exporter/nkdglgblompfaikoioecbimpbfincfea?hl=ko&utm_source=ext_sidebar)

### NotebookLM Exporter

Google NotebookLM을 애용하면서 생성된 결과물을 복사/저장하기가 불편해 시작한 프로젝트입니다.

### 프로젝트 배경

처음에는 개인 편의 도구로 만들었지만, 실제 사용자 피드백에서 동일한 수요가 확인돼 배포에 도전했습니다.

### 핵심 성과

- Google 배포 가이드라인(보안/권한/개인정보 처리)에 맞춰 여러 차례 심사 가이드를 반영했고, 검수/반려 이슈를 분석해 결국 배포까지 완료
- 현재 누적 사용자 599명+ 기준 운영되는 실서비스 형태의 도구로 유지보수 진행
- 다운로드 포맷 개선(정리된 PDF/TXT/Markdown)으로 실사용성 강화
- 현재는 꾸준히 이슈를 받아 가독성 개선을 이어가며 확장 운영

### 구현 포인트

- **다운로드 포맷 보강**: PDF, TXT, Markdown(MD) 포맷 다운로드 지원
- **가독성 개선**: 불필요한 인용 번호 정리와 문장 단위 페이지 분할로 읽기 쉬운 출력
- **한글 안정화**: 변환 과정에서 한글 깨짐 문제를 조정
- **소유자 중심의 파일화**: 확장 사용자가 바로 자산화할 수 있도록 내보내기 UX 최적화

### 결과

지금도 꾸준히 사용되는 실용 유틸로, 사용자 피드백을 받아 기능 개선 주기를 운영 중입니다.

상세 코드와 실행 로직은 깃허브([Repository](https://github.com/woolfie1101/notebooklm-exporter))에서 확인하실 수 있습니다.
