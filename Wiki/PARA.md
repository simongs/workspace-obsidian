---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Knowledge Management
related:
  - "[[LLM Wiki]]"
  - "[[Zettelkasten]]"
  - "[[Obsidian]]"
sources: []
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

PARA는 Tiago Forte가 만든 디지털 정보 정리 방법론이다. 정보를 **실행 가능성(actionability)**에 따라 4개 카테고리로 분류한다.

## 4개 카테고리

| 카테고리 | 설명 | 예시 |
|----------|------|------|
| **Projects** | 기한이 있는 단기 작업 | "Kotlin 학습", "이사 준비" |
| **Areas** | 지속적 관심 영역 | "건강", "커리어", "재정" |
| **Resources** | 관심 주제의 참고 자료 | "프로그래밍", "독서 노트" |
| **Archives** | 완료되었거나 비활성 항목 | 끝난 프로젝트, 과거 자료 |

## 핵심 철학

정보를 **어떤 종류인지**가 아니라 **지금 얼마나 실행 가능한지**로 분류한다.

## LLM Wiki에서의 구현

우리의 [[LLM Wiki]] vault에서 PARA는 **폴더가 아닌 frontmatter properties**로 구현된다:

```yaml
---
status: active       # Project (진행 중)
status: ongoing      # Area (지속적)
status: archived     # Archive (완료)
actionable: true     # 지금 실행 가능한가?
---
```

> [!warning] 왜 폴더로 구현하면 안 되는가
> Project가 끝나서 Archive로 "이동"하면 모든 `[[wikilink]]`가 깨진다. properties로 `status: archived`만 바꾸면 파일은 그대로이고 [[Obsidian]]의 Bases 뷰에서 자동으로 필터링된다.

## 관련 개념

- [[LLM Wiki]] — PARA를 properties로 녹여낸 시스템
- [[Zettelkasten]] — PARA와 직교하는 축 (분류 vs 축적)
- [[Obsidian]] — Bases 기능으로 PARA 뷰 구현
