---
layout: single
title:  "[PostgreSQL] 전문 검색(FTS) 기능 Part 3"
categories:
  - db  #카테고리
tag: [db, postgresql, fts] #태그
last_modified_at : 2025/03/23
toc: true  #오른쪽에 있는 목차
toc_sticky: true #목차 고정
author_profile: true  #왼쪽에 자기 소개란 프로필을 이 페이지에 들어갈때 끄는 기능
sidebar:
  nav: "sidebar-category" #navigation.yml에 있는 docs를 뜻한다.
search: true  #이 페이지는 검색에 나옴.
---

### (6) 한국어로도 FTS가 가능한가?


    - PostgreSQL은 기본적으로 **한국어(korean)에 대한 Full-Text Search(FLS) 기능을 제공하지만**, **영어보다 기능이 제한적.**
    - 특히 **형태소 분석(stemming), 불용어(stopwords) 처리, 구문 검색(phraseto_tsquery) 등에서 한계가 있음**.
      - PostgreSQL 14 부터 한국어에서도 phraseto_tsquery 지원이 가능하지만, 기본적인 한국어 처리 기능이 부족하여 검색 정확도가 떨어질 수 있다.
    - 한국어 전문 검색을 제대로 하려면 **한국어 형태소 분석기(별도 확장 기능(예: PGroonga))** 를 추가해야 함.


| 기능 | english | korean | 설명 |
| --- | --- | --- | --- |
| to_tsvector | 가능 | 가능 | 단어를 tsvector로 변환 |
| to_tsquery | 가능 | 가능 | 검색어를 tsquery로 변환 |
| phraseto_tsquery | 가능 | 불가능 | 한국어는 지원 안함 |
| **형태소 분석(stemming)** | 적용됨 | 적용안됨 |  |
| **불용어 처리(stopwords)** | 지원 | 지원 |  |
| **대체 해결책** | 기본지원 | **PGroonga 필요** |  |

### (7) **한글 데이터 넣고 검색해보기**

  - PGroonga 설치 방법(Mac 기준)
      
      ```bash
      brew install groonga
      brew install pgroonga
      ```
      
      ```sql
      CREATE EXTENSION pgroonga;
      --pgroonga 가 목록에 나오면 설치 완료
      SELECT * FROM pg_extension WHERE extname = 'pgroonga'; 
      ```
    

1. **데이터 생성**

    ```sql
    CREATE TABLE korean_docs (
        id SERIAL PRIMARY KEY,
        content TEXT
    );
    ```

    ```sql
    --기존 PostgreSQL의 GIN 인덱스 대신, PGroonga 전용 인덱스를 추가
    CREATE INDEX pgroonga_idx 
    ON korean_docs 
    USING pgroonga (content);
    ```

    ```sql
    INSERT INTO korean_docs (content) VALUES
    ('빠른 갈색 여우가 게으른 개를 뛰어넘다'),
    ('파란 하늘 아래에서 하얀 구름이 떠다닌다'),
    ('한국의 전통 문화는 다양하다'),
    ('기차는 철로를 따라 달린다'),
    ('백두산 천지는 아름답다');
    ```

2. 특정 단어 검색

    ```sql
    --PGroonga는 일반적인 @@ 연산자 대신 &@~ 연산자를 사용
    SELECT * FROM korean_docs 
    WHERE content &@~ '빠른';
    ```

    ![image.png](/assets/images/2025/03/23/image14.png)

3. 부분 검색 (유사어 포함)

    ```sql
    --"기차", "기차는" 같은 단어도 검색 가능
    SELECT * FROM korean_docs 
    WHERE content &@~ '기차';
    ```

    ![image.png](/assets/images/2025/03/23/image15.png)

4. 연속된 문구 검색 (구문 검색)

    ```sql
    --"파란 하늘"이라는 문구가 그대로 포함된 문서만 검색
    SELECT * FROM korean_docs 
    WHERE content &@~ '"파란 하늘"';
    ```

    ![image.png](/assets/images/2025/03/23/image16.png)

5. PGroonga 삭제 방법

    ```sql
    DROP EXTENSION pgroonga CASCADE;
    ```

## [3] 전문 검색 속도

### LIKE 검색과 전문 검색(FTS) 속도 비교 (100만개 데이터 비교)

- LIKE 속도 : 약 1327.456ms(1.3초)
- 전문 검색 속도 : 약 3.521ms(0.003초)

LIKE 검색은 전체 테이블 스캔을 하기 때문에 느리다. FTS 는 GIN 인덱스를 활용하기 때문에 좀 더 빠른 검색이 가능하다. (약 377배)

## [4] 찾아볼 만한 PostgreSQL의 추가 기능

### (1) JSON 데이터 지원

PostgreSQL은 JSON 데이터 지원하여 NoSQL처럼 사용할 수 있다.

- JSON 데이터를 저장, 조회, 필터링 가능
- JSON 컬럼을 활용하면 NoSQL과 유사한 동적 데이터 저장이 가능
- JSONB(이진 JSON) 형식을 사용하면 더 빠른 검색과 인덱싱 가능

### (2) 예제 쿼리

1. JSON 데이터 저장할 table 생성

    ```sql
    CREATE TABLE json_test (
                          id SERIAL PRIMARY KEY,
                          info JSONB  -- JSONB 타입 사용 (빠른 검색 가능)
    );
    ```

2. 데이터 삽입

    ```sql
    INSERT INTO json_test (info) VALUES
        ('{"name": "김철수", "age": 30, "skills": ["Python", "PostgreSQL"]}');
    ```

3. JSON 데이터 조회 

    ```sql
    --'->>' 연산자는 JSON 데이터를 문자열로 반환
    SELECT info->>'name' AS name FROM json_test;'
    ```

    ![image.png](/assets/images/2025/03/23/image17.png)

4. JSON 필터링

    ```sql
    SELECT * FROM json_test WHERE info->>'age' = '30';
    ```

    ![image.png](/assets/images/2025/03/23/image18.png)

5. JSON 배열 조회

    ```sql
    SELECT info->'skills'->>0 AS first_skill FROM json_test;
    ```

    ![image.png](/assets/images/2025/03/23/image19.png)

6. JSON 인덱싱 (빠른 검색)

    ```sql
    CREATE INDEX idx_users_info ON json_test USING GIN (info);
    ```

### (3) PostgreSQL(JSON) vs NoSQL

| 기능 | PostgreSQL(JSON) | NoSQL(MongoDB 등) |
| --- | --- | --- |
| SQL 지원 | 가능 | 불가능 |
| 트랜잭션 | 가능 | 일부 지원 |
| 스키마 유연성 | 가능 (JSON) | 가능 |
| 속도 | JSONB 인덱스 사용 시 빠름 | 빠름 |

즉, PostgreSQL은 RDBMS와 NoSQL의 장점을 모두 활용할 수 있다. 

- MongoDB와 SQL의 차이점
    
    
    | 기능 | PostgreSQL | MongoDB |
    | --- | --- | --- |
    | 데이터 조회 | SELECT * FROM users WHERE age > 30; | db.users.find({ age: { $gt: 30 } }) |
    | 조인 | SELECT * FROM users JOIN orders ON users.id = orders.user_id; | 조인 없음 → 중첩된 문서 사용 |
    | 트랜잭션 | 지원 | 지원 (하지만 성능 저하 가능) |
    | 스키마 | 고정된 테이블 구조 | 유연한 문서 기반 구조 |
    | 검색 속도 | 인덱스 활용, JOIN 가능 | JSON 기반 쿼리, JOIN 대신 embedded documents |
    - SQL에서 JOIN 사용 시
    
    ```sql
    SELECT users.name, orders.total
    FROM users 
    JOIN orders ON users.id = orders.user_id;
    ```
    
    - MongoDB에서는 데이터를 중첩하여 저장
    
    ```sql
    {
      _id: ObjectId("user123"),
      name: "홍길동",
      orders: [
        { total: 100 },
        { total: 200 }
      ]
    }
    ```
    

---

## [5] 마침글

그동안 PostgreSQL을 ‘무료’라서 사용한다고만 생각했지만, 이번 기회에 그 이상의 가치를 발견했다.

이제 누군가 “왜 PostgreSQL을 사용하나요?“라고 묻는다면, 그저 무료라서가 아니라, 개발자로서 충분한 이유를 가지고 내린 기술적인 결정이라고 자신 있게 답할 수 있을 것이다.

ps. 물론 한국어에 최적화되어있지 않아 추가적인 라이브러리를 설치해야하며 기능이 한정적이라는 것이 아쉽지만 글로벌을 타겟으로 하면 충분히 좋은 기능이라고 생각한다. 추가로 JSON 기능도 써보면 좋지 않을까?

----
> 다음 포스팅에서는 다른 DB종류과 PosgreSQL의 한국어FTS 대신할 Elasticsearch 에 대해 소개하겠다.