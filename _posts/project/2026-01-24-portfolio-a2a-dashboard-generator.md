---
layout: single
title:  "[포트폴리오] A2A Dashboard Generator"
date: 2025-07-01
categories:
  - project
tag: ["AI Agent", "RAG", "Automation"]
last_modified_at : 2025/07/01
toc: true
toc_sticky: true
author_profile: true
sidebar:
  nav: "sidebar-category"
search: true
---

AI Agent를 활용해 Google Sheets 요청을 받아 대시보드 이미지를 자동 생성하는 시스템입니다.
- 링크: [https://github.com/woolfie1101/a2a-dashboard](https://github.com/woolfie1101/a2a-dashboard)

## 프로젝트 개요

Google Sheets에 요청을 적재하고, AI Agent 체인에서 "요청 수집 → 데이터 추론 → 시각화 자산 생성"까지를 자동화한 프로젝트입니다.

## 문제 정의

이전에는 요청자가 직접 시각화 툴을 다루어야 했고, 데이터 컨텍스트를 매번 재해석하는 반복 작업이 많이 발생했습니다.

## 주요 구현 포인트

- **요청 인터페이스 표준화**: Google Sheets 템플릿 기반으로 요청 항목, 생성 옵션, 우선순위를 통일해 운영자가 쉽게 입력·관리할 수 있도록 구성
- **요청 전처리 자동화**: 시트 상태값을 기준으로 처리 대상만 큐로 적재하고 실패 항목은 재시도 가능한 형태로 기록
- **RAG 연동 분석**: 조회 대상 데이터를 RAG 파이프라인으로 정교화해 컨텍스트 누락을 줄이고 생성 품질을 일정 수준 유지
- **에이전트 간 역할 분리**: 수집, 분석, 이미지 생성 단계별로 역할을 분리해 오류 추적과 개선 범위를 좁힘
- **이미지 생성 자동화**: 데이터 요약 결과를 기반으로 대시보드 이미지를 자동 생성하고 산출 결과를 저장/공유 가능한 형식으로 내보내기

## 성과 및 학습 포인트

- 반복 수동 작업을 줄이고, 단일 요청 처리에 필요한 정의/분석/생성 단계를 하나의 플로우로 고도화했습니다.
- 데이터 품질 이슈가 생겨도 재처리 경로를 유지해 운영 중단 위험을 낮추는 방향으로 정비했습니다.
- 추후 동일한 패턴을 AI 광고 소재/성과 리포팅 도메인에도 확장할 수 있는 기반을 만들었습니다.
