---
layout: single
title:  "supabase + huggingface = n8n"
date: 2026-01-03
categories:
  - blog
  - 자동화
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["자동화"]
search: true
---

# supabase + huggingface = n8n

원문: [https://www.notion.so/2ddbf506e99480dbb3a3c5185ed77447](https://www.notion.so/2ddbf506e99480dbb3a3c5185ed77447)

1. [https://supabase.com](https://supabase.com/) 에 들어가서 회원가입을 합니다. 그리고 아래 사진과 같이 organization을 새로 생성합니다.

1. 프로젝트를 새로 생성합니다. (이 비밀번호를 꼭 기억하세요.)

1. 이렇게 새로 프로젝트가 나옵니다.

1. 최상단에 “Connect” 버튼 클릭

1. 아래와 같은 화면이 뜹니다.

1. `Type`을 `SQLAlchemy` 클릭

1. `Method` 를 `Transaction pooler` 로 변경

1. 아래로 스크롤을 내리면 3번의 Connect to your database의 View parameters 부분이 중요합니다.

1. [https://huggingface.co](https://huggingface.co/) 에 들어가서 회원가입을 합니다.

1. 상단 메뉴에서 “Spaces” 클릭

1. “New Spaces” 클릭

1. “Space name”에는 원하는 이름 선정, “Docker” 클릭, “Blank” 클릭 → “Create Space” 클릭

1. “Settings” 버튼 클릭

1. 아래와 같은 리스트가 뜹니다.

1. 밑에 내리다 보면 아래와 같은 부분이 나옵니다.

1. 세팅을 해줘야합니다.

1. 다 세팅을 하면 아래와 같이 리스트가 나옵니다.

1. 다시 위로 스크롤을 올려서 Files를 클릭합니다.

1. “+Contribute” 클릭 → “Create a new file” 클릭

1. “Name your file” 부분에 “Dockerfile” 이라고 적습니다.

1. 아래와 같이 Edit 창에 붙여넣습니다.

1. 아래 사진과 같이 뜨게 됩니다. 그리고 몇 분 동안 기다리면….

1. 아래 사진의 부분이 “Running” 으로 변경이 됩니다. (Running으로 바뀌지만 초기 세팅에는 10분 정도 기다려야합니다.)

1. 그러면 [https://juliet-n8n.hf.space](https://juliet-n8n.hf.space/)  이런식으로 적었던 URL을 새 창에서 띄어서 들어가게 된다면 아래와 같이 n8n을 사용할 수 있게 됩니다.

djaksfl;
