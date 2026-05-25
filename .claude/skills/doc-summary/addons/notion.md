---
name: doc-summary-notion
description: doc-summary 스킬의 Notion Tool Addon. 토글 래핑, DB/표 생성, 댓글 이관, API 제약사항.
---

# Tool Addon: Notion

doc-summary Core Workflow에 Notion 작업 시 적용하는 세부사항.

## 작업 위치

0단계에서 AskUserQuestion으로 선택형 제시:
- 원본 페이지에 직접 추가
- 개인 영역에 복사 후 작업

## 산출물 래핑

- `[Claude.hsyoo 요약]` 토글(`<details><summary>`)로 전체 산출물을 감싼다.
- 원본 콘텐츠는 토글 밖에 그대로 유지.

## 산출물 형태별

**DB인 경우:**
- inline 임베드 (is_inline: true)
- 제목은 "Claude 요약" 같은 일반명이 아닌, 문서 내용에 맞는 구체적 이름
- 기본 뷰: No ASC 정렬, 속성 표시 순서 지정

**표/텍스트인 경우:**
- 토글 내 마크다운으로 직접 작성

## 댓글 이관

- `notion-get-comments` (include_all_blocks: true)로 조회
- `notion-get-users`로 작성자 이름 확인
- 해당 항목 페이지 본문에 원문 + 요약 병기

## 제약사항 (Notion MCP API 한계)

- 이미지 (S3 pre-signed URL), 내부 페이지 링크, 임베드 객체는 복제 불가
- DB 항목 순서는 생성 순서로 보장되지 않음 → No 컬럼으로 해결
- 컬럼 너비 조절 불가
