---
layout: single
title:  "[포트폴리오] 오픈소스 활동"
categories:
  - project
tag: ["Django", "Open Source", "Merged", "Support", "Donation"]
last_modified_at : 2026/01/27
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
search: true
---


![포트폴리오 OSS](/assets/images/portfolio/elasticsearch_thumbnail.png)

### Django-CMS Core 기여 (첫 머지!)
전 세계적으로 사용되는 Django-CMS 프레임워크의 시스템 버그를 수정하고 첫 머지를 달성한 기록입니다.
- 링크: [https://github.com/django-cms/django-cms/pull/8449](https://github.com/django-cms/django-cms/pull/8449)

### 🛠 어떤 문제였나요?
Django-CMS에서 Apphook 기능을 통해 외부 모델을 연결해 사용할 때, 프론트엔드 편집 모드에서 페이지 제목(page_title)이 정상적으로 표시되지 않는 버그가 있었습니다. 원인을 파악해 보니 편집 모드의 엔드포인트에서 현재 페이지 객체를 제대로 찾아내지 못하는 문제였습니다.

### ✅ 어떻게 해결했나요?
리뷰어와 긴밀히 소통하며 아래와 같이 개선했습니다.
- **Fallback 로직 추가**: 편집 모드 진입 시 객체의 `get_absolute_url()`을 활용해 페이지 위치를 추적하는 로직을 추가했습니다.
- **최적화**: 데이터베이스 조회를 줄이기 위해 `applications_page_check`를 직접 호출하도록 구현했습니다.
- **안정성 확보**: try-finally 블록을 사용해 경로 정보를 안전하게 복구하고 테스트 코드를 보강했습니다.

### 🌟 첫 머지 소감
'내가 전 세계 사람들이 쓰는 큰 프로젝트에 기여할 수 있을까?' 하는 걱정이 앞섰지만, 메인테이너(fsbraun)와 영어로 대화하며 코드를 다듬어가는 과정은 신기하고 즐거운 경험이었습니다. 최종적으로 **Merged** 버튼이 떴을 때의 전율은 잊지 못할 것 같습니다. 제 코드가 누군가에게 도움이 될 수 있다는 사실에 가슴이 벅차오릅니다.

### 오픈소스 메인테이너 후원
더 나은 개발 생태계를 위해 헌신하는 컨트리뷰터들에게 감사의 마음을 전합니다.
- 링크: [https://github.com/sponsors/fsbraun](https://github.com/sponsors/fsbraun)

오픈소스는 수많은 사람들의 헌신으로 만들어집니다. 제가 오픈소스 기여를 통해 배운 가치만큼, 생태계를 지탱하는 다른 분들에게도 힘이 되고 싶어 후원을 진행하고 있습니다. 특히 제 첫 PR을 친절하게 리뷰해준 메인테이너에게 작게나마 감사의 표현을 전할 수 있어 기쁩니다.
