---
layout: single
title:  "[ElasticSearch] Part 1 - 설치 + 기본 사용방법 01"
categories:
  - db  #카테고리
tag: [db, elasticsearch, fts] #태그
last_modified_at : 2025/04/03
toc: true  #오른쪽에 있는 목차
toc_sticky: true #목차 고정
author_profile: true  #왼쪽에 자기 소개란 프로필을 이 페이지에 들어갈때 끄는 기능
sidebar:
  nav: "sidebar-category" #navigation.yml에 있는 docs를 뜻한다.
search: true  #이 페이지는 검색에 나옴.
---

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

| 항목   | 설명 |
|--------|------|
| **종류** | 오픈소스 검색 엔진 (Apache Lucene 기반) |
| **기능** | 빠른 검색, 자동완성, 오타교정, **필터링**, 정렬, 시각화, 로그 분석 |
| **구조** | 분산형, 확장 가능, 실시간 색인/검색 가능 |
| **연동** | PostgreSQL, MySQL, MongoDB, Kafka 등과 연동 가능 |
| **사용처** | GitHub, Netflix, Uber, Shopify, Wikipedia 등 |


### **2) PostgreSQL FTS vs Elasticsearch (한글 검색 기준)**

| 항목 | PostgreSQL FTS | Elasticsearch |
|------|----------------|----------------|
| **기본 언어 지원** | 영어/라틴어 중심 | 다양한 언어(한국어 포함) |
| **형태소 분석기** | 제한적 (예: simple, english) | 다양한 형태소 분석기 (예: **Nori**, **Arirang** 등) |
| **커스터마이징** | 비교적 어려움 | 매우 유연, custom analyzer 설정 가능 |
| **검색 정확도** | 낮음 (한국어 기준) | 높음 (동의어, 복합어 처리 등 가능) |

- **Elasticsearch + Nori 형태소 분석기 조합**은 한국어 검색에서 아주 강력하다.
    - 예시) “검색하다”, “검색”, “검색기” 같은 단어들을 잘게 쪼개고 의미 단위로 처리할 수 있어 검색 결과의 **정확도와 다양성**이 확 올라간다.

## (2) Elasticsearch의 장점

Elasticsearch는 오픈소스 분산 검색 엔진이고, 다양한 언어에 맞는 **형태소 분석기(analyzer)**를 제공하는 게 가장 큰 장점이다. 특히 한국어의 경우 [**Nori 형태소 분석기**](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-nori.html)를 제공해서 다음과 같은 이점이 있다:

- **어간 추출, 복합어 분해** 등 자연어 처리에 강하다.
- **동의어 처리, 자동 완성** 등 커스터마이징 가능.
- **검색 속도 및 정확도** 모두 우수하다.

## (3) Elasticsearch 사용 방법 미리보기

1. Docker를 통해 Elasticsearch 실행 (Nori 포함)
2. index 생성 시 custom analyzer 설정 (nori)
3. 검색 테스트 데이터 삽입
4. `match`, `term`, `analyze` API로 검색 결과 비교

---

**참고:** 글에서 Elasticsearch 를 “ES”로 줄여 부르겠다.

---

### 1) 실습 코드(미리보기)

- Docker 설정
    
    ```bash
    # docker-compose.yml
    version: '3'
    services:
      elasticsearch:
        image: docker.elastic.co/elasticsearch/elasticsearch:8.11.1
        container_name: elasticsearch
        environment:
          - discovery.type=single-node
          - xpack.security.enabled=false
        ports:
          - 9200:9200
    ```
    
- ES 인덱스 생성 및 Nori 분석기 설정 - json
    
    ```jsx
    # 인덱스 생성 + Nori 분석기 설정
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
    
- 테스트용 더미 데이터 삽입 - json
    
    ```jsx
    # 데이터 삽입
    POST /korean_posts/_doc
    {
      "content": "검색 기능을 테스트하고 있습니다."
    }
    ```
    
- 데이터 검색 - json
    
    ```jsx
    # 검색
    GET /korean_posts/_search
    {
      "query": {
        "match": {
          "content": "검색"
        }
      }
    }
    ```
    

### 2) 실습 결과 (미리보기)

- "검색"이라는 단어로 검색했을 때, "검색 기능을 테스트하고 있습니다."라는 문장이 매칭됨.
- PostgreSQL FTS에서는 매칭되지 않았던 단어가 자연스럽게 검색된다.

---

**참고:** Elasticsearch는 Kibana를 함께 사용하면 분석 결과를 시각적으로 더 잘 볼 수 있고, 쿼리 DSL에 익숙해지면 커스터마이징도 어렵지 않다.

---

## (4) Kibana란?

- Elasticsearch에 있는 데이터를 **시각화해서 보여주는 툴 이다.**
    - 데이터를 직접 검색하고, 로그/그래프/차트로 분석 가능
    - 복잡한 쿼리를 쉽게 테스트하거나 대시보드를 만들 수 있음
- 예를 들어
    - 어떤 검색어가 제일 많이 입력됐는지
    - 검색이 잘 되는지 실패하는지
    - 특정 단어가 어떤 문장들에 포함되어 있는지

이런 것들을 쉽게 확인할 수 있다.

---
> 다음 포스팅에서는 실제로 ES 실습할 수 있도록 설치부터 사용방법에 대해서 간단하게 소개하겠다.