---
categories:
  - "[[Evergreen]]"
type: entity
domain:
  - Knowledge Management
related:
  - "[[Obsidian]]"
  - "[[LLM Wiki]]"
sources: []
created: 2026-04-18
updated: 2026-04-18
tags:
  - 0🌲
  - wiki
---

## 개요

Obsidian CLI는 [[Obsidian]]이 공식 제공하는 커맨드라인 인터페이스로, vault를 터미널이나 자동화 스크립트에서 조작하게 해준다. Claude Code 같은 에이전트가 Obsidian의 네이티브 기능(wikilinks, Bases, Canvas)을 안전하게 건드릴 수 있는 관문이다.

## [[LLM Wiki]]에서의 역할

LLM이 `.md` 파일을 직접 편집할 수 있지만, CLI를 쓰면 Obsidian이 내부적으로 관리하는 링크 무결성과 캐시를 유지한 채 조작이 가능하다:

- 노트 **이름 변경 시 wikilink 자동 갱신**
- 태그/프로퍼티 조작이 Obsidian 인덱스와 동기화
- Canvas, Bases 같은 비-마크다운 포맷 수정 지원

## 주요 용도

| 용도 | 예시 |
|------|------|
| Ingest 자동화 | CLI로 Clippings 추가 + 링크 검증 |
| Lint 수행 | 고아 페이지/깨진 링크 스캔 |
| 대화 아카이브 저장 | Claude Code 세션을 `Notes/`로 커밋 |

## 관련 항목

- [[Obsidian]] — CLI의 모체 앱
- [[LLM Wiki]] — CLI를 통해 LLM이 vault를 조작하는 시스템
