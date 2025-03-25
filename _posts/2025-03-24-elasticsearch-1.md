---
layout: single
title:  "Elasticsearch(1)"
date: 2025-03-24
categories:
  - blog
  - db
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["기타", "검색(Elasticsearch)"]
search: true
---

# Elasticsearch(1)

원문: [https://www.notion.so/1c0bf506e99480b5b6f0ec998a6a3f9b](https://www.notion.so/1c0bf506e99480b5b6f0ec998a6a3f9b)

# [1] 개요

개발을 하다 보면 텍스트 검색 기능이 꼭 필요한 순간이 온다. 특히 게시글, 댓글, 상품명 등 다양한 텍스트 데이터를 효율적으로 검색하려고 PostgreSQL의 Full-Text Search(FTS)를 사용해봤는데, 한 가지 큰 문제가 있었다.

## (1) PostgreSQL FTS의 한계 (특히 한국어)

PostgreSQL FTS는 기본적으로 `to_tsvector`와 `to_tsquery`를 통해 텍스트를 토큰화하고 검색 쿼리를 수행한다. 하지만 이 기능은 **영어와 라틴계 언어와 같은 띄어쓰기가 명확한 언어에는 적합**하지만, **형태소 분석이 중요한 한국어에는 한계**가 있다.

예를 들어,

- `"검색하다"`라는 텍스트는 `"검색"`이라는 단어로는 검색되지 않는다.

- 형태소 분해가 제대로 되지 않아서 **유사 단어**나 **활용형**이 전혀 매칭되지 않는다.

한글 텍스트 검색에 대해 더 정밀하고 유연한 검색 기능이 필요하다면 Elasticsearch는 훌륭한 대안이 될 수 있다.

---

### **1) Elasticsearch란?**

---

### **2) PostgreSQL FTS vs Elasticsearch (한글 검색 기준)**

- **Elasticsearch + Nori 형태소 분석기 조합**은 한국어 검색에서 아주 강력하다.

## (2) Elasticsearch의 장점

Elasticsearch는 오픈소스 분산 검색 엔진이고, 다양한 언어에 맞는 **형태소 분석기(analyzer)**를 제공하는 게 가장 큰 장점이다. 특히 한국어의 경우 **[Nori 형태소 분석기](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-nori.html)**를 제공해서 다음과 같은 이점이 있다:

- **어간 추출, 복합어 분해** 등 자연어 처리에 강하다.

- **동의어 처리, 자동 완성** 등 커스터마이징 가능.

- **검색 속도 및 정확도** 모두 우수하다.

## (3) Elasticsearch 사용 방법 미리보기

1. Docker를 통해 Elasticsearch 실행 (Nori 포함)

1. index 생성 시 custom analyzer 설정 (nori)

1. 검색 테스트 데이터 삽입

1. `match`, `term`, `analyze` API로 검색 결과 비교

---

**참고:** 글에서 Elasticsearch 를 “ES”로 줄여 부르겠다.

---

### 1) 실습 코드(미리보기)

- Docker 설정

- ES 인덱스 생성 및 Nori 분석기 설정 - json

- 테스트용 더미 데이터 삽입 - json

- 데이터 검색 - json

### 2) 실습 결과 (미리보기)

- "검색"이라는 단어로 검색했을 때, "검색 기능을 테스트하고 있습니다."라는 문장이 매칭됨.

- PostgreSQL FTS에서는 매칭되지 않았던 단어가 자연스럽게 검색된다.

---

**참고:** Elasticsearch는 Kibana를 함께 사용하면 분석 결과를 시각적으로 더 잘 볼 수 있고, 쿼리 DSL에 익숙해지면 커스터마이징도 어렵지 않다.

---

## (4) Kibana란?

- Elasticsearch에 있는 데이터를 **시각화해서 보여주는 툴 이다. **

- 예를 들어

이런 것들을 쉽게 확인할 수 있다.

# [2] ES 실습하기

## (1) Docker 설치하기(MacOS 기준)

### 1) **터미널에서 Docker 설치하기 (Homebrew 사용)**

1. 먼저 Homebrew가 설치돼 있는지 확인

1. Docker 설치(Docker Desktop 설치 - GUI포함)

1. 설치 후 실행(회원가입 및 로그인 진행)

### 2) **docker-compose.yml 파일 만들기**

1. 실습용 폴더 만들기

1. docker-compose.yml 파일 생성 및 열기

1. 파일에 해당 내용 붙여넣기

### 3) **Docker Compose로 실행하기**

1. compose 파일이 있는 폴더에서 아래 명령어 입력

### 4) **브라우저에서 확인**

1. 이제 아래 주소로 접속

- ES 상태 확인 👉 [http://localhost:9200](http://localhost:9200/)

- Kibana 웹 UI 👉 [http://localhost:5601](http://localhost:5601/)

### 5) **Nori 플러그인 포함된 Docker 이미지 설치**

1. 폴더와 파일 만들기

1. **아래 내용을 **Dockerfile에** 붙여 넣고 저장, 닫기**

### 6) **docker-compose.yml 수정 (커스텀 이미지 사용)**

1. 기존  ES 설정을 아래처럼 바꾸기

### 7) **기존 컨테이너 정리 및 커스텀 이미지 빌드 + 컨테이너 실행**

1. 정리

1. 빌드 및 실행

1. 실행 상태 확인

## (2) 인덱스 생성 및 데이터 조회하기

### 1) **Nori 분석기 적용 인덱스 만들기**

- 사용할 도구

1. 인덱스 생성 쿼리 (korean_posts라는 이름)

1. 실행 완료 시 나오는 값

### 2) 인덱스 생성 쿼리 분석

```json
PUT /korean_posts
{
  "settings": {
    "analysis": {
      "analyzer": {
        "korean": {
          "type": "custom",
          "tokenizer": "nori_tokenizer"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": {
        "type": "text",
        "analyzer": "korean"
      }
    }
  }
}
```

- 인덱스 생성 쿼리 분석

### 3) **한글 데이터 넣기**

우리가 만든 korean_posts 인덱스에 실제 한글 문장을 몇 개 넣어보기위해 이번엔 POST 요청으로 문서(=글)를 추가한다.

- 예시 데이터 삽입 (Kibana Dev Tools에서 실행)

- 쿼리 실행 후 결과

### 4) **검색 테스트 (match 쿼리)**

- 쿼리 실행(Kibana Dev Tools에서 실행)

- 쿼리 실행 후 결과

- 추가 테스트 : “빠르게”로 검색하면 형태소 분석하여 “빠른” 단어가 있는 해당 글을 검색

## (3) 생성한 인덱스 조회 및 삭제

### 1) 인덱스 조회

1. 인덱스 목록 조회 (간단한 방법)

1. 결과 예시

1. 자주 쓰이는 cat API

### 2) 인덱스 삭제

1. 전체 인덱스 삭제 (주의!)

1. 조건에 맞는 여러 인덱스 삭제

### 3) 인덱스 수정

- 기존 인덱스의 mapping, analyzer 같은 건 수정 불가

- 인덱스 구조(settings, mappings)는 생성이후에는 변경이 거의 불가능하다.

- 특히 tokenizer, analyzer 같은 설정은 변경 불가

대신 방법이 있다.

`새 인덱스 만들고 데이터 마이그레이션` 하는 것이다.

예시)

1. articles_ko_v2라는 새 인덱스 생성

1. 기존 인덱스에서 데이터 _reindex API로 복사

1. 필요하면 기존 인덱스 삭제

- POST /_reindex 란?

## (4)  문서 수정 및 삭제

### 1) 문서 수정(부분 수정)

```json
POST /index_name/_update/doc_id
```

예시)

```json
POST /articles_ko/_update/abc123
{
  "doc": {
    "title": "수정된 제목입니다"
  }
}
```

문서 ID가 `abc123` 인 문서의 title만 수정하는 방식이다.

### 2) 문서 삭제

```json
DELETE /index_name/_doc/doc_id
```

예)

```json
DELETE /articles_ko/_doc/abc123
```

# [3] 마무리하며 – 실습 후 느낀 점

필자는 PostgreSQL FTS의 한계로 인해 한국어 검색에서 원하는 결과를 얻기 어려웠고,

이를 보완하기 위해 ES와 Nori 형태소 분석기를 실험해보았다.

직접 실습을 통해:

- Nori 분석기가 문장을 형태소 단위로 잘게 나누고

- "검색", "분석" 같은 단어로도 다양한 문장이 매칭되는 것

을 확인할 수 있었다.

물론, ES를 처음 쓰는 입장에서는 약간의 러닝커브가 있지만,

한글 검색의 정확도와 유연성을 고려한다면 도입할 가치가 충분하다고 느꼈다.

다음 단계에서는 autocomplete, 동의어 사전 등을 활용한 **더 정교한 검색 경험**을 구현해볼 계획이다.
