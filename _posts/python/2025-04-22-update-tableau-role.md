---
layout: single
title:  "[Python] Python으로 Tableau 사용자 130,000명의 역할을 자동으로 변경한 이야기"
categories: 
    - Python
    - tableau  #카테고리
tag: [python, tableau] #태그
toc: true  #오른쪽에 있는 목차
toc_sticky: true #목차 고정
author_profile: true  #왼쪽에 자기 소개란 프로필을 이 페이지에 들어갈때 끄는 기능
sidebar:
    nav: "sidebar-category" #navigation.yml에 있는 docs를 뜻한다.
search: true  #이 페이지는 검색에 나옴.
---

![이미지](/assets/images/2025/04/22/pytho.png)


## 💡 문제 상황

최근 Tableau 관리 업무 중,
**130,000명에 달하는 사용자들의 역할(site role)**을 일괄 변경해야 할 일이 생겼다.

수작업으로는 현실적으로 불가능했으며,
실수 없이 정확하게 조건에 맞는 사용자만 필터링하고, 안전하게 변경할 수 있는 방법이 필요했다.

## 🎯 목표

	•	역할이 ExplorerCanPublish인 사용자만 추출
	•	이메일 도메인이 특정 조건(@example.com)을 만족하는 경우만 포함
	•	예외 조건(@partner.example.com이 이름에 포함된 사용자 제외) 처리
	•	변경 전 CSV 백업
	•	테스트 모드에서 10명만 우선 변경
	•	전체 결과 로깅

## 🛠️ 해결 방법: Python + tableauserverclient

Python의 tableauserverclient 라이브러리를 사용해 아래 기능들을 하나씩 구현했다.

	•	서버 접속 및 인증
	•	사용자 전체 목록 조회
	•	조건 필터링 로직 작성
	•	역할 변경 함수 구현
	•	백업 CSV 생성 및 로그 저장

```python
def should_update_user(user):
    return (
        user.site_role == 'ExplorerCanPublish' and
        user.name.endswith('@example.com') and
        '@partner.example.com' not in (user.fullname or '').lower()
    )
```

### 🧪 테스트 모드 실행 예시

```bash
python update_user_roles.py --preview
python update_user_roles.py --update --role Explorer --test
```

	•	결과는 test_users_preview_20240422.csv 등의 파일로 저장되며
	•	로그에는 사용자명, 역할, 마지막 로그인 시점 등이 남는다.

### 📁 전체 사용자 역할 변경 실행

```bash
python update_user_roles.py --update --role Explorer
```

## ✅ 결과
	•	전체 약 130,000명의 사용자 중 약 7,800명이 조건에 부합
	•	테스트 모드에서 10명 선별 후, 전체 롤아웃 적용
	•	작업 시간: 약 5분
	•	기존 수작업 예상 소요 시간 대비 수십 배 이상 시간 절약

## 🔒 안전장치
	•	백업 CSV 생성 (변경 전 데이터 보존)
	•	예외 처리 및 실패 로그 저장
	•	CLI 옵션을 통한 테스트/실행 모드 구분

## 📌 마무리하며

자동화는 개발자가 편해지기 위해서만 존재하지 않다.
단순 반복 업무를 줄이고, 더 중요한 일에 집중할 수 있도록 도와준다.

이번 경험을 통해 “Python으로 얼마나 많은 업무를 덜어낼 수 있는지” 다시금 체감할 수 있었다.


## 📂 전체 코드 보기

해당 자동화 스크립트의 전체 소스 코드는 아래 GitHub 저장소에서 확인하실 수 있다.

👉 🔗 [GitHub - tableau-role-updater](https://github.com/woolfie1101/realworld-snippets/tree/main/python/tableau-role-updater)

	•	실제 실행 예시
	•	테스트 모드 옵션
	•	로그 및 CSV 파일 생성 방식

까지 모두 포함되어 있으니 참고하면 된다.
