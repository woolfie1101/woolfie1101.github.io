---
layout: single
title:  "PostgreSQL 연동"
date: 2025-03-25
categories:
  - blog
  - db
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["기타", "데이터베이스(PostgreSQL)"]
search: true
---

# PostgreSQL 연동

원문: [https://www.notion.so/1c1bf506e99480918175cf570b3326a9?pvs=1](https://www.notion.so/1c1bf506e99480918175cf570b3326a9?pvs=1)

**🧩 연동 방법**

## (1) 애플리케이션 레벨 연동

**🧪 1단계: 연동 흐름 이해**

간단하게 예를 들어볼게:

```plain text
[PostgreSQL]
title: "인공지능 기술의 미래"
content: "인공지능은 의료, 교육..."
case_number: 21

↓

[Elasticsearch]
Index: articles_filter_test
Fields: title, content, case_number
```

**📌 2단계: 연동 코드 흐름 (Node.js 예시)**

```javascript
// 1. PostgreSQL에서 데이터 조회
const articles = await pgClient.query("SELECT * FROM articles");

// 2. Elasticsearch에 데이터 전송
for (const article of articles.rows) {
  await esClient.index({
    index: "articles_filter_test",
    id: article.id,
    document: {
      title: article.title,
      content: article.content,
      case_number: article.case_number,
    },
  });
}
```

**🧰 3단계: 필요한 npm 패키지**

```bash
npm install @elastic/elasticsearch pg
```

**✅ 목표**

PostgreSQL DB의 데이터를 조회해서 → Elasticsearch 인덱스에 저장하는 Spring 서비스 만들기.

---

**🧱 1단계: 기본 준비**

**1. 의존성 추가 (Gradle 기준)**

```bash
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'
    runtimeOnly 'org.postgresql:postgresql'
}
```

---

**📄 2단계: Entity & Repository 구성**

**PostgreSQL 엔티티**

```bash
@Entity
@Table(name = "articles")
public class Article {
    @Id
    private Long id;

    private String title;
    private String content;
    
    @Column(name = "case_number")
    private Integer caseNumber;

    // Getters, Setters
}
```

**Elasticsearch 문서 모델**

```bash
@Document(indexName = "articles_filter_test")
public class ArticleDocument {

    @Id
    private String id;

    private String title;
    private String content;

    @Field(type = FieldType.Integer)
    private Integer caseNumber;

    // Getters, Setters
}
```

---

**📂 3단계: Repository**

```bash
public interface ArticleRepository extends JpaRepository<Article, Long> {}
public interface ArticleElasticRepository extends ElasticsearchRepository<ArticleDocument, String> {}
```

---

**🔁 4단계: PostgreSQL → Elasticsearch 전송 로직**

```bash
@Service
@RequiredArgsConstructor
public class ArticleSyncService {

    private final ArticleRepository articleRepository;
    private final ArticleElasticRepository articleElasticRepository;

    public void syncToElasticsearch() {
        List<Article> articles = articleRepository.findAll();

        List<ArticleDocument> docs = articles.stream()
                .map(a -> {
                    ArticleDocument doc = new ArticleDocument();
                    doc.setId(a.getId().toString());
                    doc.setTitle(a.getTitle());
                    doc.setContent(a.getContent());
                    doc.setCaseNumber(a.getCaseNumber());
                    return doc;
                }).collect(Collectors.toList());

        articleElasticRepository.saveAll(docs);
    }
}
```

---

**🚀 5단계: 테스트 API 만들기**

```bash
@RestController
@RequiredArgsConstructor
public class SyncController {

    private final ArticleSyncService articleSyncService;

    @PostMapping("/sync")
    public ResponseEntity<String> sync() {
        articleSyncService.syncToElasticsearch();
        return ResponseEntity.ok("Sync complete!");
    }
}
```
