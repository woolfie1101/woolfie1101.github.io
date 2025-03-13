---
layout: single
title:  "[PostgreSQL] 전문 검색(FTS) 기능 Part 1"
categories:
  - db  #카테고리
tag: [db, postgresql, fts] #태그
last_modified_at : 2025/03/13
toc: true  #오른쪽에 있는 목차
toc_sticky: true #목차 고정
author_profile: true  #왼쪽에 자기 소개란 프로필을 이 페이지에 들어갈때 끄는 기능
sidebar:
  nav: "sidebar-category" #navigation.yml에 있는 docs를 뜻한다.
search: true  #이 페이지는 검색에 나옴.
---
# PostgreSQL 전문 검색 Part 1


필자가 다니는 회사에서 가장 많이 사용하는 데이터베이스 관리 시스템은 PostgreSQL이다.

가장 많이 사용하는 이유가 ‘무료’라고 생각을 했었다.

하지만, 그게 다가 아닐 것이라는 생각이 문득 들었다.

그래서 이번 기회에 내가 모르는 PostgreSQL의 기능 중에 가장 놀랍고 사용하기 좋은 한 가지를 찾아 소개를 하고자 한다.

## [1] PostgreSQL을 사용하는 이유

1. 오픈소스이면서 강력한 기능 제공
    1. 오픈소스 데이터베이스지만, 상용 데이터베이스에 버금가는 강력한 기능 제공
    2. 예시로는 ACID 트랜잭션 지원, 다양한 인덱싱 방식, JSONB 등
    - ACID 트랜잭션 지원

      데이터베이스에서 트랜잭션(한 번에 실행되는 작업 단위)이 안전하게 수행되도록 보장하는 4가지 특성:

        - **Atomicity(원자성)**: 트랜잭션이 **완전히 실행되거나 아예 실행되지 않음**
        - **Consistency(일관성)**: 트랜잭션 실행 후 **데이터 무결성이 유지됨**
        - **Isolation(고립성)**: 여러 트랜잭션이 동시에 실행될 때 **서로 간섭하지 않음**
        - **Durability(지속성)**: 트랜잭션이 성공적으로 끝나면 **데이터가 영구적으로 저장됨**
    - 다양한 인덱싱 방식

      PostgreSQL은 여러 종류의 인덱스를 제공하는데, 사용 목적에 따라 다름.

        - **B-Tree (기본 인덱스)**: 숫자, 문자열 정렬/검색에 가장 일반적
        - **GIN (Generalized Inverted Index)**: JSONB, 배열, 풀텍스트 검색에 최적화
        - **GiST (Generalized Search Tree)**: 공간 검색(예: 좌표 데이터)
        - **BRIN (Block Range INdex)**: 대용량 테이블에서 범위 검색할 때 사용

        ```sql
        # 예시
        CREATE INDEX idx_gin ON users USING GIN (tags);
        # 'tags' 컬럼이 배열이면 GIN 인덱스로 빠르게 검색 가능
        ```

2. 데이터 무결성과 안정성
    1. 트랜잭션이 강력하고 WAL(write-ahead logging) 방식으로 장애 복구가 가능해서 데이터 무결성이 뛰어남
    2. 복잡한 쿼리에서도 높은 신뢰성을 보장(WAL로 장애복구, ACID 트랜잭션 보장, 복제 및 백업 기능 제공)
    - WAL
        - 데이터를 변경하기 전에 로그를 먼저 기록하는 방식
        - 장애 발생 시 로그를 보고 복구 가능
    - 데이터 무결성
        - 데이터가 정확하고 일관되며 손상되지 않도록 보장
3. 확장성과 성능
    1. 대량의 데이터를 처리할 때 성능이 뛰어나고, 파티셔닝, 병렬 쿼리 실행 등의 기능이 있어 확장성이 좋음
    - 대량 데이터 처리
        - 효율적인 인덱스 사용으로 빠른 검색
        - 병렬 쿼리로 여러 CPU가 동시에 연산
        - 파티셔닝을 이용해 테이블을 여러개로 나누어 성능 최적화

        ```sql
        PARTITION BY RANGE(date) # 파티셔닝(2024, 2025)
        SET max_parallel_workers_per_gather = 4; # 병렬 쿼리
        ```

4. JSONB 등 다양한 데이터 타입 지원
    1. 관계형 데이터 뿐만 아니라 JSONB 같은 비정형 데이터도 효율적으로 저장하고 쿼리 가능
    2. RDBMS이지만 NoSQL 기능도 활용 가능하여 유연한 데이터 모델링이 가능
    - RDBMS와 NoSQL 비교
        - **RDBMS (관계형 데이터베이스)**: 정형 데이터, 테이블 기반, SQL 사용 (예: PostgreSQL, MySQL)
        - **NoSQL (비관계형 데이터베이스)**: 비정형 데이터, 스키마 유연함, 다양한 저장 방식 (예: MongoDB, Redis)

      | 특징 | RDBMS | NoSQL |
              | --- | --- | --- |
      | 데이터 구조 | 테이블 | 문서(JSON), 키-값, 그래프 |
      | 스키마 | 고정 | 유연 |
      | 확장성 | 수직 확장 | 수평 확장 |
      | 트랜잭션 | 강한 ACID 보장 | 느슨한 일관성 |
5. 커뮤니티 지원
    1. 커뮤니티가 활발하여 문제 해결이 쉬움

## [2] 소개하고자 하는 기능

위에서 소개한 PostgreSQL의 장점 중 하나인 `다양한 인덱싱 방식`에 해당하는 `전문 검색`에 대해서 소개를 하겠다.

### (1) 전문 검색(Full-Text Search, FTS)이란

RDBMS 에서 특정 단어에 대한 패턴 검색을 할 경우 LIKE와 ‘%’를 대게 사용한다.

```sql
SELECT * FROM documents WHERE content LIKE '%play%';
```

이렇게 사용을 하면 ‘app’ 이 포함된 모든 문자열이 조회된다. 예를 들어 ‘playground’, ‘playlist’, ‘gameplay’ 등등 이 나온다.
이러면 정작 찾아야하는 ‘play’의 뜻에 맞는 단어들이 나오지 않고 생뚱맞은 단어가 나오게 되어 정확도가 떨어지며, 와일드카드를 사용하게 되어 데이터 양이 많은 경우 검색 속도가 현저하게 저하하게 된다.

일반적인 LIKE 검색보다 강력한 기능을 제공하는 것이 바로 전문 검색이다.
대량의 테스트 데이터에서 효율적으로 원하는 정보를 찾을 수 있도록 도와준다.

전문 검색에 대해서 짧게 설명을 하자면 다음과 같다.
예를 들어 “playing” 이라고 검색을 하면 “play”, “plays”, “played” 가 들어간 데이터를 찾아준다.
이는 <풀텍스트 검색(Full-text search)> 기능을 활용하면 전문 검색이 가능해진다.

- GIN 인덱스를 활용해 검색 성능 향상
- 검색 정확성 (apple > apples, pineapple의 차이를 인식하여 더 정확한 결과를 반환)
- 어간 추출과 언어 처리 (run > running, runs 등 언어 조회 가능)
- 유사한 단어 검색 (cat > cats, kittens 등 관련된 결과 조회 가능)
- `to_tsvector`, `to_tsquery`, `phraseto_tsquery` 사용

### (2) 전문 검색 종류

1. **to_tsvector(텍스트를 검색 가능한 형태로 변환)**
    1. **기능**
        1. 문장을 **토큰화(단어 단위로 분리)하고, 불용어 제거(the, a …) 및 어간(stemming) 추출**
        2. 데이터베이스에 **저장할 때 변환**해서 **검색을 빠르게 수행하도록 인덱싱**
        3. 보통 **문서 저장 시 변환하여 tsvector 컬럼에 저장**
    2. **언제 사용?**
        1. 검색 속도를 빠르게 하기 위해 **미리 변환한 형태(tsvector)를 저장**할 때
        2. GIN 인덱스를 적용하여 빠르게 검색하고 싶을 때
2. **to_tsquery (검색어를 검색 가능한 형태로 변환)**
    1. **기능**
        1. 사용자가 입력한 검색어를 **검색 가능한 형태(tsquery)로 변환**
        2. 검색어에도 **어간(stemming) 적용 가능**
        3. 논리 연산자(&, |, !)를 사용하여 **복잡한 검색 가능**
    2. **언제 사용?**
        1. to_tsvector로 변환된 데이터에서 **검색을 수행할 때**
        2. @@ 연산자와 함께 사용하여 **빠른 검색을 수행할 때**
3. **phraseto_tsquery (연속된 단어 검색)**
    1. **기능**
        1. 연속된 단어를 검색할 수 있도록 tsquery 변환
        2. "quick brown" → 'quick' <-> 'brown' 형태로 변환됨
        3. 즉, 단어 **순서까지 고려한 검색을 수행할 때 사용**
    2. **언제 사용?**
        1. "quick brown"처럼 **연속된 단어(구문)를 검색할 때**
        2. "quick"과 "brown"이 **떨어져 있으면 검색되지 않도록 제한하고 싶을 때**

| 기능 | 언제 사용?  | 예제 |
| --- | --- | --- |
| to_tsvector | 데이터 저장시 변환 (색인 생성) | to_tsvector('english', content) |
| to_tsquery | 검색어 변환 (검색할 때 사용) | to_tsquery('english', 'quick & fox') |
| phraseto_tsquery | 연속된 단어 검색 (구문 검색) | phraseto_tsquery('english', 'quick brown') |


----
> 다음 포스팅에서는 실제 전문 검색을 할 수 있는 예제와 필자가 직접 수행한 실제 결과를 사진으로 첨부해 작성하여 소개하겠다.