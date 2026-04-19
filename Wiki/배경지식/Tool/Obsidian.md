---
categories:
  - "[[Evergreen]]"
type: entity
domain:
  - Knowledge Management
related:
  - "[[LLM Wiki]]"
  - "[[Steph Ango]]"
  - "[[Obsidian Bases]]"
  - "[[Obsidian CLI]]"
sources: []
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 개요

Obsidian은 로컬 마크다운 파일 기반의 지식 관리 도구다. "파일 우선(file-first)" 철학으로, 모든 노트가 plain text `.md` 파일로 저장된다.

## LLM Wiki의 이상적인 저장소인 이유

1. **마크다운 기반** — LLM이 직접 읽고 쓸 수 있는 형식
2. **wikilinks (`[[]]`)** — 노트 간 연결을 자동 추적. 이름 변경 시 자동 업데이트
3. **그래프 뷰** — 지식의 구조를 시각화. 허브 노드, 고아 페이지 한눈에 파악
4. **git 호환** — vault가 그냥 폴더이므로 버전 관리, 백업, 동기화 가능
5. **[[Obsidian Bases]]** — frontmatter 기반 동적 뷰. 속성 기반으로 폴더 없이 재조합
6. **[[Obsidian CLI]]** — CLI로 vault 조작 가능. Claude Code와 연동

## 핵심 Obsidian 전용 문법

| 문법 | 용도 |
|------|------|
| `[[wikilink]]` | 내부 노트 연결 |
| `![[embed]]` | 다른 노트/이미지 임베드 |
| `> [!type]` | Callout (강조 박스) |
| YAML frontmatter | 노트 메타데이터 (properties) |
| `==highlight==` | 형광펜 |

## [[Steph Ango]]의 Vault 철학

Obsidian CEO가 직접 공유한 원칙들:
- **폴더 최소화** — "많은 노트가 하나 이상의 사고 영역에 속한다"
- **properties로 분류** — `categories` frontmatter + Bases로 가상 분류
- **링크와 백링크로 탐색** — 파일 탐색기가 아닌 Quick Switcher, 백링크로 이동
- **Fractal journaling** — 일일 → 주간 → 월간 → 연간 리뷰

## 관련 항목

- [[Steph Ango]] — Obsidian CEO, vault 구조 원칙 제시
- [[Obsidian Bases]] — frontmatter 기반 동적 뷰
- [[Obsidian CLI]] — CLI로 vault 조작
- [[LLM Wiki]] — Obsidian을 저장소로 활용하는 지식 시스템
