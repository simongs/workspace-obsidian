---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Knowledge Management
related:
  - "[[LLM Wiki]]"
  - "[[PARA]]"
  - "[[Obsidian]]"
sources: []
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

Zettelkasten(제텔카스텐)은 독일어로 "슬립 박스(slip box)"를 의미한다. 사회학자 Niklas Luhmann이 고안한 지식 관리 방법론으로, **원자적(atomic) 노트를 링크로 연결하여 상향식으로 지식을 축적**한다.

## 3가지 노트 유형

| 유형 | 설명 | 수명 |
|------|------|------|
| **Fleeting note** (일시적) | 떠오른 생각을 빠르게 기록 | 일시적 — 나중에 정리하거나 폐기 |
| **Literature note** (문헌) | 외부 소스 요약. 원본 참조 포함 | 반영구적 |
| **Permanent note** (영구) | 자신만의 언어로 정제한 원자적 아이디어 | 영구적 |

## 핵심 원칙

- **원자성 (Atomicity)** — 하나의 노트에 하나의 아이디어만
- **링크 중심** — 분류는 폴더가 아닌 연결(link)로
- **자기 언어** — 복사가 아닌 자신의 말로 재구성
- **상향식 (Bottom-up)** — 카테고리를 미리 정하지 않고, 노트가 쌓이면서 구조가 자연 발생

## LLM Wiki에서의 매핑

우리의 [[LLM Wiki]] vault에서 Zettelkasten의 흐름은 다음과 같이 구현된다:

```
Fleeting (Daily/, Notes/)  →  Literature (Clippings/)  →  Permanent (Wiki/)
    내가 쓴다                    내가 수집한다               LLM이 정제한다
```

전통 Zettelkasten에서는 사람이 Permanent note를 직접 쓰지만, LLM Wiki에서는 **LLM이 이 과정을 대신**한다.

## 관련 개념

- [[LLM Wiki]] — Zettelkasten + LLM 자동화
- [[PARA]] — 실행 가능성 기반 분류 (Zettelkasten과 직교하는 축)
- [[Obsidian]] — Zettelkasten 구현에 이상적인 도구 (wikilinks, 그래프 뷰)
