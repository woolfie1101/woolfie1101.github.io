---
layout: single
title:  "Elasticsearch(2)"
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

# Elasticsearch(2)

원문: [https://www.notion.so/1c0bf506e994807a9499fe4dde83b4a1](https://www.notion.so/1c0bf506e994807a9499fe4dde83b4a1)

# [1] 개요

이번 장에서는 ES의 기본적인 검색 기능을 넘어서 실전 서비스에서 사용하는 고급 검색 기능, PostgreSQL과의 연동 방식까지 함께 살펴 볼 것이다.

## (1) 주요 목표

- 정확한 검색 (match_phrase)

- 오타 교정 (fuzzy)

- 검색어 강조 (highlight)

- 검색어 우선순위 설정 (boost)

- 조건 필터링 (filter)

- 자동완성 (autocomplete)

- 인기 키워드 집계 (aggregation)

- 시간대별/조건별 Kibana 시각화

- PostgreSQL과의 데이터 연동

- 다국어(한국어 + 영어) 동시 검색

---

# [2] 고급 검색 기능

## (1) 자동 완성

자동완성은 보통 검색어 앞부분만 입력해도 추천리스트가 뜬다. 

예시) “검” 입력 → “검색”, “검토”, “검사” 추천

이걸 구현하려면 ES에서 **edge_ngram tokenizer**를 사용한 **custom analyzer**를 설정해야한다.

### 1) 자동완성 전용 인덱스 만들기

1. 인덱스 설정

1. 인덱스 분석

### 2) **자동완성용 데이터 삽입**

autocomplete_test 인덱스에 아래처럼 추천 단어들을 넣어보자.

실제 검색어 추천 리스트라고 생각하면 된다.

- Kibana Dev Tools에서 실행

### 3) **자동완성 검색 테스트**

- 이제 "검"만 입력했을 때, 위의 추천어들이 잘 매칭되는지 테스트해보자

- “겅” 이라고 입력했을 경우, 값이 나오질 않는다.

- “색” 이라고 입력했을 경우, 값이 나오질 않는다.

- edge_ngram은 앞글자 기준으로 추천을 한다.

### 4) 자동완성 추천어의 구성 방법

실습하면 알게되는 사실이 있다. 그 사실은 바로, 자동완성 추천어를 구성하려면 해당 추천어들을 추가해야한다는 것이다. 

한국어에서 사용하는 단어는 대략 511,000개 이상이다. 
그럼, 이 단어들을 전부 넣어야하는 걸까? 
결론을 말하자면, 이 단어들을 전부 넣지 않아도 된다. 

**서비스에서 사람들이 검색할 가능성이 높은 데이터**를 넣으면 충분하다.

---

- 실무에서 추천어를 구성하는 방법

---

예를 들어 필자가 만든 웹앱에서 검색이 필요한 대상이

- 커뮤니티 → 게시글 제목, 키워드

이라면 이 **“제목”** 들을 미리 인덱싱해서 자동완성 대상으로 쓰면 된다.

---

예시로 이런 것도 가능하다.

> PostgreSQL DB에 있는 title 컬럼을 기준으로 매일 1회 자동으로 ES에 추천어 업데이트

→ 이 결과를 스크립트로 ES에 넣고 자동완성 추천용으로 사용하면 된다.

아니면, 검색 로그를 분석해서 사용자들이 자주 쓰는 단어들로 유지하여도 충분하다.

## (2) **오타 교정 기능 (Fuzzy Matching)**

예: 사용자가 "샤이닝"이라고 잘못 입력했을 때,
→ ES가 "샤이니"로 유사한 단어를 찾아주는 기능!

- 오타 교정에는 2가지 방식이 있다.

### 1) **예제 인덱스 만들기 (fuzzy_test)**

```json
PUT /fuzzy_test
{
  "mappings": {
    "properties": {
      "name": {
        "type": "text"
      }
    }
  }
}
```

- “setting” 이 없어도 되는 이유

- “setting”이 필요한 때 → custom analyzer를 직접 설정하고 싶을 때 필요

### 2) **데이터 삽입**

```json
POST /fuzzy_test/_doc
{ "name": "샤이니" }

POST /fuzzy_test/_doc
{ "name": "아이유" }

POST /fuzzy_test/_doc
{ "name": "방탄소년단" }
```

### 3) **Fuzzy 검색 쿼리**

```json
GET /fuzzy_test/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "샤이닝",
        "fuzziness": "AUTO"
      }
    }
  }
}
```

![image](/assets/images/notion/2025/03/24/image-2025-03-24-elasticsearch-2.md-0-f1748490f23f.png)

- "샤이닝" → "샤이니"로 유사도 기반 매칭 성공!

- 쿼리 분석

- fuzziness 상세 설명

- 참고 옵션

## (3) 대량 데이터 삽입, 검색 성능 테스트, relevance 확인

### 1) Relevance 에 대해서

- Relevance란?

- **예시**

- 결과

- 분석

- Elasticsearch는 relevance를 어떻게 계산하는가?

- match 쿼리 작동 순서

- 정리

### 2) Relevance 실습 내용 리스트

- 우리가 할 실습

- 준비할 내용

### 3) **인덱스 생성 (예: articles_nori_phrase_v2)**

```json
PUT /articles_nori_phrase_v2
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "nori_with_phrase": {
          "type": "nori_tokenizer",
          "decompound_mode": "mixed"
        }
      },
      "analyzer": {
        "nori_phrase_analyzer": {
          "type": "custom",
          "tokenizer": "nori_with_phrase",
          "filter": [ "lowercase" ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "nori_phrase_analyzer"
      },
      "content": {
        "type": "text",
        "analyzer": "nori_phrase_analyzer"
      }
    }
  }
}
```

- `"decompound_mode": "mixed"` 옵션 설명

- 요약

### 4) **샘플 JSON 데이터 준비 및 삽입**

1. 샘플 JSON 데이터 추가

### 5) **[Relevance 실험1] match vs match_phrase**

- 실험 목표

1. match 쿼리 (단어 포함만 확인)

1. match_phrase 쿼리 (단어 순서까지 맞아야 함)

1. match vs match_phrase 실험 결과

1. 어떻게 사용해야하나?

1. 실전 사용 방법

1. fallback이란?

### 6) **[Relevance 실험2] boost (title vs content relevance)**

- 실험 목표

- boost 를 사용하는 이유

- 전체 데이터 삭제 (인덱스 유지)

- 샘플 데이터 추가 (확실한 비교를 위해 넣음)

1. 기본 쿼리 (boost 안 한 상태)

1. 결과

1. boost 추가한 쿼리

1. 결과

1. 실험 결과 분석

1. 실전에서 boost를 사용하는 이유

1. 실전 쿼리 예시 : title boost + phrase fallback까지 포함

### 7) **[Relevance 실험3] bool 쿼리로 검색 + 필터 조합하는 실습**

- 실험 목표

- 전체 데이터 삭제 (인덱스 유지)

- 샘플 JSON 데이터 추가

1. 예시 쿼리 (must + must_not)

1. 결과

1. must_not 없앨 경우 결과

### 8) **[Relevance 실험4]  filter, range, sort를 조합해서 속도 빠르고, 정확도 높은 검색 만들기**

- Elasticsearch에서 검색 결과를 정렬하고 필터링할 때는 두 가지 방식이 있다.

- 실험 목표

1. 먼저 전처리

1. 새 인덱스 만들기 : articles_filter_test

1. articles_diverse_bulk.json 파일 다운로드 및 추가

1. 삽입 후 필터 + 정렬 쿼리 실행

1. 결과

1. 5가지 기능을 추가해 검색해보기

## (4) 검색 로그 기반 추천어 구성

- 목표

- 로직

### 1) 검색 로그 인덱스 만들기

- 인덱스 설정

### **2) 검색할 때마다 로그 저장하기**

- 이건 실제 서비스에선 백엔드에서 자동으로 처리됨

- timestamp에 ISO 8601 형식의 날짜를 넣는 걸 잊지 말아야 한다.

- 아래 예시에서 keyword, timestamp만 다르게 해서 넣어보자

### **3) 인기 검색어 집계하기 (예: 상위 5개)**

```json
GET /search_logs_v2/_search
{
  "size": 0, //문서 본문은 필요없고 집계 결과만 가져온다 
  "aggs": { //집계 섹션 시작 (여기에 그룹 통계, 평균, 최대값 등 계산)
    "popular_keywords": { //이 이름으로 aggregation 결과를 저장
      "terms": { //terms 집계: 특정 필드 값별로 그룹화
        "field": "keyword.keyword", //집계 기준 필드
        "size": 5 //가장 많이 등장한 상위 5개만 가져오기
      }
    }
  }
}
```

- 결과

- “size”: 3 → 가장 최근에 검색된 3개의 문서도 같이 보임(본문도 보인다.)

### **4) 자동완성 인덱스에 등록하기**

- **📌 자동완성은 completion 필드를 사용해**

- 위에서 소개한 edge_ngram 방식과 completion 필드 방식의 차이점

- 인기 검색어들을 반복해서 추가하면 된다.

### **5) 자동완성 불러오기(추천어)**

- 예시: 자동완성 쿼리

- 지금 서비스처럼 만들기 위해 필요한 것

- **🎁 보너스 쿼리: 최근 7일간 인기 검색어만 보기**

## (5) **Kibana 시각화 대시보드 만들기**

- 목표

### **1) Kibana 접속**

1. 브라우저에서 [http://localhost:5601](http://localhost:5601/) 로 접속

1. 왼쪽 메뉴에서 **“Analytics” → “Discover”** 클릭

### **2) Index Pattern 생성**

(처음 생성이면 이걸 먼저 해야 한다.)

1. **왼쪽 메뉴 → “Management” → “Stack Management”** 클릭

1. **“Kibana” → “Data Views” → Create Data view** 클릭

1. 이름: search_logs_v2 (또는 사용 중인 인덱스 이름)

1. 인덱스 패턴: search_logs_v2* 또는 정확한 인덱스 이름 입력

1. Timestamp 필드가 있다면 선택 (@timestamp 같은 거)

1. 저장 (Save data view to Kibana)

### **3) Discover 탭에서 데이터 확인**

![image](/assets/images/notion/2025/03/24/image-2025-03-24-elasticsearch-2.md-1-be2428874303.png)

- 데이터가 안 나온다면 시간 범위 늘리기

- 결과

### **4) Visualize Library →  최근 7일간 인기 검색어 차트 만들기 (Bar chart 기준)**

1. Analytics → Visualize Library → Create new visualization

1. “Lens” 시각화 방식 선택(가장 직관적이고, 드래그앤드롭 기반)

### **5) Dashboard에 추가해서 시각화 구성하기**

1. field 값 설정 

# [3] 마무리하며 – 실습 후 느낀 점

ES에 정말 많은 기능이 있다는 것을 알 수 있었다. 이런 것들이 다 무료라는 것이 놀라울 따름이다. 

다음 단계에서는 이번 장에서 배운 기능들이 다 들어가 있는 통합 인덱스(영어, 한글, 영어+한글)와 Spring Data Elasticsearch로 ES API 호출을 구현해볼 계획이다. 마지막으로는 ES 를 사용해야할 이유와 Mysql vs ES vs Pgsql 의 차이점도 다뤄볼 생각이다.
