---
layout: single
title:  "Elasticsearch(3)"
date: 2025-03-27
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

# Elasticsearch(3)

원문: [https://www.notion.so/1c3bf506e99480fa969af84d5ed3c707](https://www.notion.so/1c3bf506e99480fa969af84d5ed3c707)

# [1] 개요

이번 장에서는 전 장에서 배운 ES의 기능들을 통합하여 “한국어, 영어, 한국어+영어” 종류별로 하나의 인덱스, 하나의 조회 쿼리를 만들어 볼 것이다.

그리고 서비스를 만들어 Spring Data Elasticsearch 로 ES API 호출을 직접해보고, ES를 사용해야하는 이유 에 대해서 정리할 것이다.

마지막으로 MySQL vs ES vs PgSQL 을 비교 분석을 끝으로 ES의 대장정을 마칠 것이다.

## (1) 지금까지 실습한 검색 기능 목록

# [2] **전체 기능을 포함한 통합 인덱스 설계**

## (1) 한국어 전용 통합 인덱스 설계

### **1) 인덱스**

1. 인덱스 설계

1. 인덱스 분석

1. 샘플 데이터 삽입

### **2) 데이터 조회 쿼리**

1. 데이터 조회 쿼리

### **3) 데이터 조회 결과**

```json
{
  "took": 42,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "articles_ko",
        "_id": "DHeH2pUBeb73f_H6EnPD",
        "_score": null,
        "_source": {
          "title": "기술의 발전",
          "content": "인공지능은 미래 사회를 변화시킬 것이다.",
          "case_number": 2,
          "timestamp": "2025-03-24T10:00:00+09:00"
        },
        "highlight": {
          "title": [
            "기술<mark>의</mark> 발전"
          ],
          "content": [
            "<mark>인공지능</mark>은 <mark>미래</mark> 사회를 변화시킬 것이다."
          ]
        },
        "sort": [
          2
        ]
      },
      {
        "_index": "articles_ko",
        "_id": "C3eH2pUBeb73f_H6EnPD",
        "_score": null,
        "_source": {
          "title": "인공지능의 미래",
          "content": "이 기술은 다양한 산업에 적용된다.",
          "case_number": 1,
          "timestamp": "2025-03-25T08:00:00+09:00"
        },
        "highlight": {
          "title": [
            "<mark>인공</mark><mark>지능</mark><mark>의</mark> <mark>미래</mark>"
          ]
        },
        "sort": [
          1
        ]
      }
    ]
  }
}
```

## (2) 영어 전용 통합 인덱스 설계

### 1) 인덱스

1. 인덱스 설계

1. 인덱스 분석

1. 샘플 데이터 삽입

### 2) 데이터 조회 쿼리

1. 데이터 조회 쿼리

### 3) 데이터 조회 결과

```json
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "articles_en",
        "_id": "EXcg25UBeb73f_H6ZHMu",
        "_score": null,
        "_source": {
          "title": "The future of AI technology",
          "content": "AI technology is transforming the world.",
          "case_number": 42,
          "timestamp": "2025-03-26T10:30:00+09:00"
        },
        "highlight": {
          "content": [
            "<mark>AI technology</mark> is transforming the world."
          ]
        },
        "sort": [
          42
        ]
      }
    ]
  }
}
```

### 4) (추가) 자동완성 인덱스 예시

1. 인덱스 생성

1. 데이터 추가(자동완성에 쓰일 데이터)

1. 데이터 조회(단어 일부만 적을 시 나올 데이터)

1. 데이터 조회 결과

### 5) (추가) **최근 7일간 인기 검색어 집계**

1. 데이터 추가(검색을 할 때마다 키워드 저장)

1. 데이터 조회(7일간 인기 검색어 순위 조회)

1. 데이터 조회 결과

## (3) 다국어 전용 통합 인덱스 설계 - 1 (한국어 / 영어 따로)

### 1) 인덱스

1. 인덱스 설계

1. 인덱스 분석

1. 샘플 데이터 삽입

### 2) 데이터 조회 쿼리

1. 데이터 조회 쿼리

### 3) 데이터 조회 결과

```json
{
  "took": 10,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 4,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "articles_ko_en",
        "_id": "F3c125UBeb73f_H6NnMP",
        "_score": null,
        "_source": {
          "title_en": "Autonomous Vehicles",
          "content_en": "Self-driving cars are powered by AI and sensors.",
          "case_number": 4,
          "timestamp": "2025-03-23T18:15:00+09:00"
        },
        "sort": [
          4
        ]
      },
      {
        "_index": "articles_ko_en",
        "_id": "Fnc125UBeb73f_H6NnMP",
        "_score": null,
        "_source": {
          "title_en": "Future of AI",
          "content_en": "AI is transforming various industries like healthcare and finance.",
          "case_number": 3,
          "timestamp": "2025-03-24T09:00:00+09:00"
        },
        "sort": [
          3
        ]
      },
      {
        "_index": "articles_ko_en",
        "_id": "FXc125UBeb73f_H6NnMP",
        "_score": null,
        "_source": {
          "title_ko": "인공지능과 의료",
          "content_ko": "인공지능이 의료 산업에 큰 영향을 미치고 있다.",
          "case_number": 2,
          "timestamp": "2025-03-25T14:30:00+09:00"
        },
        "highlight": {
          "content_ko": [
            "<mark>인공</mark><mark>지능</mark>이 의료 산업에 큰 영향을 미치고 있다."
          ]
        },
        "sort": [
          2
        ]
      },
      {
        "_index": "articles_ko_en",
        "_id": "FHc125UBeb73f_H6NnMP",
        "_score": null,
        "_source": {
          "title_ko": "자율주행차의 미래",
          "content_ko": "자율주행차는 인공지능 기술로 발전하고 있다.",
          "case_number": 1,
          "timestamp": "2025-03-26T10:00:00+09:00"
        },
        "highlight": {
          "content_ko": [
            "자율주행차는 <mark>인공</mark><mark>지능</mark> <mark>기술</mark>로 발전하고 있다."
          ]
        },
        "sort": [
          1
        ]
      }
    ]
  }
}
```

## (4) 다국어 전용 통합 인덱스 설계 - 2 (한국어 + 영어 혼합)

### 1) 인덱스

1. 인덱스 설계

1. 인덱스 분석

1. 샘플 데이터 삽입

### 2) 데이터 조회 쿼리

1. 데이터 조회 쿼리

### 3) 데이터 조회 결과

```json
{
  "took": 26,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "mixed_articles",
        "_id": "GHdH25UBeb73f_H6onOp",
        "_score": null,
        "_source": {
          "title": "AI 기술이 발전하고 있습니다.",
          "content": "인공지능(AI)과 머신러닝 기술이 산업에 큰 영향을 미치고 있다.",
          "case_number": 50,
          "timestamp": "2025-03-25T08:30:00+09:00"
        },
        "highlight": {
          "title": [
            "<mark>AI 기술</mark>이 발전하고 있습니다."
          ]
        },
        "sort": [
          50
        ]
      }
    ]
  }
}
```

### 4) 최근 7일간 인기 검색어 집계 예시 (search_logs 인덱스 기준)

```json
GET /search_logs/_search
{
  "size": 0,
  "query": {
    "range": {
      "timestamp": {
        "gte": "now-7d/d"
      }
    }
  },
  "aggs": {
    "popular_keywords": {
      "terms": {
        "field": "keyword.keyword",
        "size": 10
      }
    }
  }
}
```

# [3] 실제 서비스에서 ES 사용방법

## (1) ES 를 사용하는 방법

## (2) Spring 서비스와 RDB, ES간의 흐름

### 1) 게시글 등록 시 자동 동기화 흐름

1. 다이어그램

1. 설명

### **2) 검색 API 통합 흐름**

1. 다이어그램

1. 설명

### 3) 자동 삭제 흐름 (동기화 유지)

1. 다이어그램

1. 설명

## (3) Spring Data Elasticsearch를 이용해 ES 사용

- 참고사항

### 1) SpringBoot 세팅

1. 의존성 추가

1. ES 설정 (application.yml)

### 2) Spring Data Elasticsearch를 이용해 데이터 저장 및 검색

1. Entity

1. Repository

1. Service

1. Controller

## (4) Spring에서 조회용 복잡한 쿼리 만들기

Spring Data Elasticsearch는 JPA와 비슷한 Repository 형태로 ES에 접근할 수 있게 도와주지만, 
복잡한 쿼리 (bool, multi_match, fuzzy, highlight, range, sort 등)는 ElasticsearchOperations 와 DSL(QueryBuilders) 로 작성해야한다.

- ElasticsearchOperations와 QueryBuilders 란?

### **버전 1) search() 메서드 내에서 NativeSearchQueryBuilder().build()를 바로 사용**

위에서 `[2]-(4) 다국어 전용 통합 인덱스 설계 - 2 (한국어 + 영어 혼합)`에서 했던 데이터 조회 쿼리를 직접 Java로 작성을 해보겠다.

1. 조회 쿼리 작성

1. 주요 포인트

### **버전 2) NativeSearchQuery를 변수로 먼저 선언하고 search()에 전달**

1. Entity

1. Service

1. Controller

# [4] ES와 RDB의 관계, ES를 사용해야하는 이유

## (1) RDB와 ES 를 같이 사용하는 이유

필자는 이 실습을 하면서 이런 궁금증이 생겼다. 

> ES를 DB처럼 똑같이 데이터를 넣으니까 ES만 사용하면 되는게 아닐까? 왜 동일한 데이터를 두 곳에 저장을 할까?”

### 1) RDB**과 ES의 차이점**

### 2) 왜 ES와 RDB을 같이 사용하고 중복 저장을 할까?

1. 역할이 다르기 때문이다.

1. 효율성 때문이다.

### 3) 중복 저장이 불편한데 어떻게 해결 할 수 있을까?

- 단점

- 해결

### 4) 실무에서는 어떻게 사용하나?

- 거의 대부분의 회사들이 RDB + ES 병행 구조를 사용한다.

- 예시

### 5) 검색 정보는 ES에만 저장하고 그외 정보는 RDB에만 저장할 수 없을까?

> “검색을 위해 필요한 정보(제목, 내용, 카테고리)는 Elasticsearch에만 저장하고, 그 외의 정보(사진, 링크, 작성자 등)는 RDB에만 저장해도 될까?”

- 결론부터 말하자면

- 이유

- 데이터 저장 추천구조

- 예시

### 6) **Elasticsearch + PostgreSQL 연동의 이점 요약**

## (2) ES 를 사용해야 하는 이유

### 1) ES 도입 배경 및 목적

- 커뮤니티 서비스, 검색 기반 콘텐츠 플랫폼, 대량 게시글/댓글/로그 데이터가 있는 서비스는 빠르고 정확한 검색 기능이 필수이다.

- 기존 RDB 기반 LIKE 검색은 느리고 확장성이 부족하며, 추천/자동완성/오타교정 기능 구현이 어렵다.

- ES + Kibana 도입을 통해 성능, UX, 인사이트 분석을 동시에 개선이 가능하다.

### 2) **비용 (Elasticsearch & Kibana)**

- Elastic의 “Basic License”까지는 무료지만, 엔터프라이즈 기능 (보안, 머신러닝 등)은 유료이다.

- 일반적인 검색 + 시각화 + 로그 수집 + 추천 기능은 모두 무료 범위에서 가능.

### 3) **사용해야 하는 이유**

### 4) **기존 PostgreSQL vs Elasticsearch 비교**

### 5) 결론

1. ES는 단순한 검색 엔진이 아니라 사용자 경험, 성능, 데이터 분석까지 아우르는 핵심 인프라이다.

1. ES를 사용하는 곳

# [5] kibana에서 제공하는 시각화를 화면에 제공할 수 있나?

Kibana에서 만든 시각화(Visualizations)나 대시보드(Dashboards)를 API를 통해 외부 화면에 보여줄 수 있다.

### **방법 1) Kibana Embed (iframe 방식)**

가장 간단하고 대표적인 방식이다.

1. 시각화 저장 후 “Share”

1. Kibana에서 시각화 또는 대시보드 열기

1. 우측 상단 [Share] 버튼 클릭

1. “Embed code” (iframe) 선택

1. iframe 태그 복사해서 프론트엔드 페이지에 붙이기

- **주의사항**

---

### **방법 2) Saved Object → API 조회**

Kibana 7.x 이상에서는 저장된 시각화를 API로도 조회할 수 있다.

- 예시: 저장된 대시보드 목록 조회

- 단점

---

### **방법 3) Elasticsearch로 직접 차트 데이터 뽑기**

Kibana 없이도 가능하다

1. Kibana에서 사용한 쿼리를 기억하거나 export해서

1. Spring 서버에서 Elasticsearch API 호출하여

1. 데이터를 직접 받아서 Chart.js, ApexCharts 같은 걸로 시각화 가능

- 예: 인기 키워드 Top 5

# [6] 마무리 글

## **🎯 마무리하며**

이번 학습을 통해 단순 키워드 매칭을 넘어, **자연어 기반의 정교한 검색 시스템을 직접 설계하고 구현**해보았다.

Elasticsearch의 핵심 기능인 match, match_phrase, fuzzy, boost, filter, aggregation, suggest 등을 조합하여 실제 사용자 중심의 **검색 품질 향상과 UX 개선**을 고려한 쿼리 구조를 구성할 수 있었다.

또한 **자동완성, 하이라이팅, 다국어(한글+영어) 검색,**

**Spring Boot 기반의 실시간 저장 및 검색 API 개발**,

**Kibana를 활용한 시각화 대시보드 구성**까지 실습하면서

**검색과 데이터 흐름 전반을 아우르는 통합 아키텍처의 기반을 구축**했다.

---

## **🌱 앞으로 확장할 계획**

이번 프로젝트에서 다루지 못했지만, 다음과 같은 고급 기능들을 향후 확장할 계획이다:

- **Synonym 사전 커스텀 (분야별 검색 품질 개선)**

- **Elastic Security / Log / APM 모듈 도입 (xpack, user role 등)**

- **Kafka 기반의 비동기 동기화 구조**

- **클러스터 구성 실습 및 고가용성 구조 설계(다중노드, 샤드/레플리카 전략)**

- **NLP 기반 검색 (BERT Reranking, Elastic ML)**

- **실제 서비스 적용 시의 트러블슈팅 및 최적화**

---

> 필자는 이제 검색을 단순한 기능이 아닌, 
**서비스 품질의 핵심 요소로 인식하고 설계할 수 있는 수준으로 성장했다. 
**앞으로도 이러한 검색 기능을 실제 서비스에 적용해보며, 현실적인 문제를 해결하고, 사용자에게 더 나은 검색 경험을 제공할 수 있는 개발자로 나아갈 것이다.
