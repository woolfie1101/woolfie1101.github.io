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

Google NotebookLM을 애용하면서 생성된 결과물을 복사하거나 저장할 방법이 없어 불편함이 컸고, 그 경험에서 시작한 프로젝트입니다.

#### 프로젝트 배경
처음에는 개인 사용 편의를 위해 시작했지만, 곧 다른 사용자들도 이 기능이 필요할 것으로 판단해 배포에 도전했습니다.

### 핵심 성과
- 호기롭게 시작해 배포 과정을 거치며 구글의 보안·배포 가이드라인을 만족시키기 위해 여러 번의 검토 과정을 진행했습니다.
- 거절(Reject)이 여러 번 나왔지만, 원인을 분석하고 보완해 최종적으로 배포를 완료했습니다.
- 현재도 꾸준히 사용되는 실용적인 도구로, 사용자 피드백을 반영하며 기능을 개선해왔습니다.

### 구현 포인트
- **진짜 내 파일로 소유하기**: PDF, TXT, Markdown(MD) 포맷으로 완벽하게 다운로드 가능
- **가독성 향상**: 불필요한 인용 번호 제거로 텍스트 정리
- **한글 안정성**: 변환 시 한글 깨짐 문제 해결
- **페이지 분할 최적화**: 문장 단위로 페이지를 깔끔하게 분할해 읽기 쉬운 저장본 제공

상세 코드와 실행 로직은 깃허브([Repository](https://github.com/woolfie1101/notebooklm-exporter))에서 확인하실 수 있습니다.
