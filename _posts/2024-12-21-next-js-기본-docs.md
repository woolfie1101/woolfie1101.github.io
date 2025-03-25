---
layout: single
title:  "Next.js 기본 Docs"
date: 2024-12-21
categories:
  - blog
  - javascript
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["기타", "프론트엔드(Next.js)"]
search: true
---

# Next.js 기본 Docs

원문: [https://www.notion.so/15abf506e99480129bb2dee23fb53f98?pvs=1](https://www.notion.so/15abf506e99480129bb2dee23fb53f98?pvs=1)

# 환경 구성

vscode ctrl , + tab size 검색 2

nvm use v20, nvm install 20.18.1 ([설치](https://github.com/coreybutler/nvm-windows/releases))

bun install on mac

```javascript
brew install oven-sh/bun/bun
```

bun install on windows(관리자 아님)

```javascript
powershell -c "irm bun.sh/install.ps1|iex"
```

Bun requires a minimum of Windows 10 version 1809

setup(15에서는 터보팩이 들어가나보다)

shadcn 뉴욕, slate 선택

```javascript
bunx --bun shadcn --version
```

```javascript
bunx create-next-app@15.0.4 next-example
cd next-example
bunx --bun shadcn@2.1.6 init
// Default, base color slate
// code . or open folder in vscode
```

vscode ext - tailwind, Material Icon Theme Philipp Kief, Simple React Snippets Burke Holland, Prisma

```javascript
// 데이터 공유 끄기
bun next telemetry disable
```

명령어

```javascript
bun run dev
bun run build
bun start

bunx --bun shadcn@2.1.6 add button
```

DB 설치 - [https://www.enterprisedb.com/downloads/postgres-postgresql-downloads](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads) 

비번 1234 기준, DB next (SQL Shell psql) create database next;

---

# 첫 페이지

# 로그인 페이지

로그인 페이지 생성, 새로만든거 잘 안되면 bun run dev 재시작

카드 콤포넌트 추가 

```javascript
// bunx --bun shadcn@2.1.0 add card
bunx --bun shadcn@2.1.6 add --all
```

로그인 페이지 확인해보자

Header 생성

```javascript
bun add react-icons // 아이콘
```

하단 영역에 가입 링크 부분을 추가한다. 콤포넌트로 가입 화면에서는 로그인으로 이동하도록 한다.

로그인에 필요한 스키마를 만들고 정의하고 useForm 사용

```javascript
bun add @radix-ui/react-icons
```

오류, 성공 표시 콤포넌트 

로그인 처리 액션, 

이제 로그인 해보면 서버쪽에 로그 찍힌다

# 회원가입

이제 로그인페이지에서 계정이 없나요 누르면 나옴

화면 끝

# 디비 연동 

```javascript
bun add -d prisma
bun add @prisma/client
```

.ignore 파일에 .env 도 추가해놓는다(15부터 기본)

```javascript
bunx prisma init
```

```javascript
bunx prisma generate // 타입 생성
bunx prisma db push // 디비 직접 작업 
bunx prisma studio // 테이블, 데이터 확인, 참고용
```

auth 연동 관련, 암호화 관련 , next-auth

```javascript
bun add @auth/prisma-adapter
bun add bcryptjs
bun add -d @types/bcryptjs
bun add next-auth@beta
```

진짜 가입 로직 

진짜 가입해보자. 디비에 데이터 들어가 있다. 

사용자 정보 가져오는 액션 /data  생성

# NextAuth 설정

![image](https://prod-files-secure.s3.us-west-2.amazonaws.com/d9230c5b-f4f1-4a8d-8fca-b34a051da56f/b7a1dfe3-a704-4a71-8d21-c6e8c8644e0f/image.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=ASIAZI2LB466QSLQPAZ2%2F20260325%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20260325T131344Z&X-Amz-Expires=3600&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEOX%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLXdlc3QtMiJIMEYCIQDn5O6xLvcCqufK2ap9hlTnbbgja1%2FRPjWnRE%2FYpsoSWgIhANvcbt3gN6fRRFRNSBYhK5SkJbhpocEK8T9IiPucg6oCKogECK7%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEQABoMNjM3NDIzMTgzODA1IgzWYFjJGkzQ5of7DHAq3APEn8wehi22g7EpVtskOmgNeQbPr8e%2FNVe78V53tXrLJea4GHbqppts1Btjzcr8J9ZM1eJHDhJtcm%2BjqIJTkvU2L%2BcP2dDWdNw178bUNVD2xDCXrRfVfe51ROtyK%2B%2BeF5lagIEbQM1fMtcVmUiJo%2FSFMclTcMwsTV%2B0%2Bi%2BTXuU2u1Zl2NRtzdYN4wsZ%2BguP7DLO9CfbCeeaDStbh9pJGX94VMCynE70PyJa%2B2ytdIEtGriZPIobA2GnlxAo%2B%2BuDBmGEJjsvqxA1Lmx63Gr6LYQMZNu0emIWDsYLWFT7UIVHDpPA7cHW3aPO20lulV4tNrQv2HtIWZZBRDfBF32jsBIfFbyDQFzip6wNEjml6T2r8lE%2F8gtSwSeD1WFB6qvoM2%2BEVrPuoKa29B1JvzavXOvIere5FtOung4aDVKfy7KEjzD9QAH4uM0YETgJgY1IgEp%2FMLqfQeQWSD%2B2lBENCsvHgYdQqmdMLf1p2NuzIpKnIEQuvMkEUcx%2B06iax%2BorWtWhdvFdltbNN7vdbGHVIzVsnukM9YiPzFwzZJxXza%2FqN8l8Guu8n5O%2FtQMm6n%2BfhE0UfS7X21VWukP2RBPeDKNnkWW5qKYBWB3cme3Hy5mFvqdL28%2FMF4SjQM3ICzDFqI%2FOBjqkASe76GUF5SEGlIrKi70XdZeEvo7Z%2BnbICSjdGZoMMB1C9qKW2JGLxON8uSCREMtiSuwsB0RWFiP8jYAad0KZgGr%2B5dA9Sa12Vj%2BciXQI8whrM20pC3L1jlRUAg2uyoKqV%2BCOsRFbbXUf%2BaVqnbSnql3%2FfFmIPvE6i66xhTACl41HnWpQjpsIpja1aOE%2FFTIV7LEHcKEObvO3pcWC3WaGtntMoHkG&X-Amz-Signature=49f7e610b163e1df621e6dd3ca87a4841f7b4ced5f2291aa111ca9623b3e3903&X-Amz-SignedHeaders=host&x-amz-checksum-mode=ENABLED&x-id=GetObject)

```javascript
NEXTAUTH_SECRET=your-random-secret-key
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

[http://localhost:3000/api/auth/providers](http://localhost:3000/api/auth/providers) 접속해보면 설정 확인 가능

주소 상수

로그인 해보자 

# 인증 추가 작업

### 로그아웃

### 사용자 버튼

로그인 끝

---

# 레이아웃

/app/page.tsx 를 /app/(dashboard)/ 폴더 만들어 그 아래로 옮긴다

## 사이드바

사이드바 자리 표시


사이드바 콤포넌트

[https://logoipsum.com/](https://logoipsum.com/) 아무 로고 가져와본다

/public 폴더 만들어 /public/logo.svg 파일 생성 (복붙)

### 로고

### 사이드바 메뉴

2차 변수로 

이제 사이드바에서 버튼으로 동작은 한다

search 페이지도 만들어서 왔다 갔다 해보자, 검색 글자가 있는데 Sidebar에 가려서 안보일거임

검색 페이지에 가면 검색이라는 글자가 안 보임. 사이드바 만큼 벌어지게 해야 함

---

### 상단 Navbar

### 모바일용 사이드바

### 상단 네비바 메뉴
