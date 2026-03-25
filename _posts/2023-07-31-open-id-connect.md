---
layout: single
title:  "Open ID Connect"
date: 2023-07-31
categories:
  - blog
  - spring-security
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["기타", "보안/인증"]
search: true
---

# Open ID Connect

원문: [https://www.notion.so/57d07f135c9d4d5da34d4533b78d82fa](https://www.notion.so/57d07f135c9d4d5da34d4533b78d82fa)

# [1] 개요 및 특징

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-0-e8bd79d7fa45.png)

- OAuth 2.0 은 인증이 아니라 인가를 위해서 있는 것이다.

- OpenID Connect Protocol 은 인증을 위해 ID Token을 받는 것이다. 

- ID Token에는 User 정보가 들어있다. 

---

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-1-e555d1e67d64.png)

# [2] ID Token & Scope

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-2-bc1c3085ceb0.png)

- access Token 은 인증되었다는 것을 증명할 수 없다. ID token은 인증 되었음을 증명할 수 있다.

- access Token  없이 ID token 만으로도 인증을 할 수 있다. 

- ID token는 유저 정보를 가져올 수 없다.

---

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-3-6475a9355ac6.png)

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-4-e32e4ed9293c.png)

- 여기서 신뢰 당사자란 클라이언트를 뜻한다.

- OpenID Provide : 예시로 구글, 페이스북 등등

- relying party : 클라이언트

- 흐름 4번만 해도 인증이 가능하다.(ID Token 만 사용해서도 가능)

---

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-5-39d683921ee5.png)

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-6-7406c2b9cad1.jpg)

scope에 openid를 추가하니 response에 id_token이 추가됨을 확인 할 수 있다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-7-612042075621.jpg)

id_token의 값(jwt)을 풀면 이렇게 정보(claim)가 나온다. 이로 인증처리가 가능하다.

access_token으로도 비슷하게 나온다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-8-ed5496e4f7bd.jpg)

---

access_token으로 해당 정보를 가져올 수 있다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-9-3ce8779fae9d.png)

id_token으로는 유저 정보를 가져올 수 없다는 것을 확인 할 수 있다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-10-0c40f934cc97.png)

공개클라이언트에서 response_type을 id_token으로 하고 nonce를 추가하면 id_token이 발급되고

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-11-d1eb83f971b2.png)

공개클라이언트에서  response_type을 token으로 하면 access_token이 발급된다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-12-255316878680.png)

공개클라이언트에서  response_type을 code으로 하면 code가 발급된다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-13-15aa4f75b4d8.png)

공개클라이언트에서  response_type을 code, token, id_token 로 하면 3개 다 발급받을 수 있다. 

![image](/assets/images/notion/2023/07/31/image-2023-07-31-open-id-connect.md-14-3b9084852b75.png)

현재 스프링시큐리티는 code만 가능하다

키크론과 같이 3개 다 지원하면 사용 가능하다.
