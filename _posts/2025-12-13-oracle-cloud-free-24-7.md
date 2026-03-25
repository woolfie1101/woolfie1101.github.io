---
layout: single
title:  "Oracle Cloud Free (24/7)"
date: 2025-12-13
categories:
  - blog
  - aws
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
tag: ["AWS"]
search: true
---

# Oracle Cloud Free (24/7)

원문: [https://www.notion.so/2c8bf506e99480f9a89ec160742308d5](https://www.notion.so/2c8bf506e99480f9a89ec160742308d5)

# [1] ⚠️ 오라클 클라우드 가입 시 주의사항 (필독)

- 오라클 클라우드 가입시 주의사항 (무조건 한 번에 성공해야함. - 안그러면 골치아파짐)

- 가입 오류 발생 시 대처 방안

# [2] 오라클 클라우드 가입

1. 오라클 클라우드 주소로 접속

1. “무료로 시작하기” 버튼을 클릭

1. 이름, 성, 이메일 주소를 입력하고 수신된 이메일의 링크를 클릭하여 인증을 완료 (꼭 이메일을 확인)

1. 비밀번호와 클라우드 계정 이름을 설정

1. 일반인 계정으로 선택O, 기업 선택X

1. 홈 영역을 선택 

1. 주소 정보를 전부 영문으로 변환하여 입력

1.  신용카드 정보를 입력하여 지급 확인 단계를 거침. 이 과정에서 오라클 클라우드에서 돈이 승인 되었다가 즉시취소가 된다. 걱정 말라.

# [3] 오라클 클라우드 유료 계정 전환

1. 로그인을 하면 상단에 이렇게 뜬다. Upgrade 클릭

1. 개인 계정으로 선택해서 계정을 만들었기 때문에 `Individual` 클릭

1. 2번 사진에서 체크해야하는 두 부분을 다 한다. Tax details 에서 select 박스에서 not registration 을 클릭

1. 그러면 아래와 같은 페이지가 뜬다.

1. 여기서 이 유료 계정 전환하기까지 시간이 걸린다. 필자는 24시간 후에 확인을 해보니 유료 계정 전환이 되어있었다.

# [4] 무료 인스턴스 생성

1. 상단의 햄버거 버튼 클릭

1. `Compute` 버튼 클릭 → `Instances` 버튼 클릭

1. `create instance` 클릭

1. `Name` 에 원하는 이름으로 지정, `Availability domain`은 1~3개 로 구성되어 있는데 아무거나 고르면 된다. 

1. `Change image` 클릭

1. `Ubuntu` 선택

1. `Canonical Ubuntu 22.04 Minimal aarch64` 선택 (Price가 Free인 것으로 다른 것을 선택해도 무방)

1. `Change shape` 클릭

1. `Virtual machine`, `Ampere` 선택

1. 아래와 같은 상태면 된다. 하단의 `Next` 클릭

1. `Security` 부분이 아래 사진과 같은 상태인지 확인 후 하단의 `Next` 클릭

1. `Create new virtual cloud network` 클릭, `Create new public subnet` 클릭

1. 밑으로 내려가서 첫번째 박스 클릭

1. `Download private key` , `Download public key` 클릭. 다운로드받은 이 파일은 절대 잃어버리면 안된다!! 이것으로 서버 로그인이 가능하다.

1. `Next` 버튼을 누르면 아래와 같은 예상 비용 계산기가 나온다. 우리는 무료 인스턴스를 사용하기 때문에 걱정하지 않아도 된다. 계정 전체를 합쳐서 스토리지 총량이 무료 분을 넘는다면 요금이 나온다. 하지만 이번에 생성한 것은 1 OCPU에 6GB RAM 이기 때문에 걱정하지 않아도 된다.

1. `Next` 버튼을 누르면 인스턴스 생성이 완료 된다.

1. 아래와 같이 인스턴스 생성이 완료된 것을 볼 수 있다.

# [5] 인스턴스 사용하기

## (1) Public IP address 생성

1. `Details` 를 눌러서 해당 인스턴스의 스펙을 확인 할 수 있다. 보면 `Public IP address` 에 값이 없는 것을 볼 수 있다.

1. `Networking` 버튼을 누른다.

1. public 고정 IP 를 만들어야한다. Name에서 인스턴스 이름을 클릭한다.

1. 그러면 아래와 같은 화면이 뜬다.(화면이 원래 영어로 뜨는데 번역을 써서 한국어가 섞여있다.)

1. `IP administration` 메뉴를 클릭한다.

1. `Edit` 버튼을 누른다.

1. `Ephemeral public IP` 클릭 > 하단의 `Update` 클릭

1. 아래와 같은 화면이 보이면 다시 뒤로 (Attached VNICs 클릭)

1. `Public IP address` 부분에 값이 생겼다.

## (2) 인스턴스 연결하기

1. 아래 링크(오라클 클라우드 공식 문서)에 나온대로 linux 인스턴스에 연결하기를 따라하면 된다.

1. 필자는 Mac 이므로 Mac 으로 설명하겠다.

1. 그리고 `Public IP address` 에 나온 값을 아래와 같은 명령어를 친다.

1. `yes` 입력

1. 이러면 이제 인스턴스에 접속하여 사용할 수 있다.
`ssh -i [pub key 파일 경로] ubuntu@[Public IP address]` 로 인스턴스 접속이 언제든 가능하다.
