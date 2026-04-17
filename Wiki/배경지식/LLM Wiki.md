---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Knowledge Management
related:
  - "[[Zettelkasten]]"
  - "[[PARA]]"
  - "[[Andrej Karpathy]]"
  - "[[Obsidian]]"
sources:
  - "[[LLM Wiki - Karpathy Gist 요약]]"
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

LLM Wiki는 [[Andrej Karpathy]]가 제안한 개인 지식 관리 패턴이다. LLM이 **위키를 자동으로 작성하고 유지보수**하며, 사람은 소스를 큐레이션하고 질문하는 역할을 맡는다.

## 핵심 아이디어: 컴파일된 지식

기존 RAG(Retrieval-Augmented Generation)는 질문할 때마다 원본에서 검색하여 답변을 생성한다. 매번 지식을 "인터프리팅"하는 셈이다. LLM Wiki는 다르다 — 원본 소스를 읽고 미리 정리해서 **위키로 컴파일**해놓는다.

| RAG (기존) | LLM Wiki |
|---|---|
| 질문할 때마다 원본에서 검색 | 원본을 읽고 미리 정리해놓음 |
| 지식이 매번 휘발됨 | 지식이 축적되고 복리로 성장 |
| 교차 참조 없음 | 문서 간 링크와 관계가 이미 구축됨 |
| 모순점 발견 못함 | 새 정보가 기존과 충돌하면 표시 |

## 3층 구조

```
1. Raw Sources   — 원본 소스 (불변, LLM은 읽기만)
2. Wiki          — LLM이 생성/관리하는 마크다운 파일들
3. Schema        — CLAUDE.md 같은 운영 규칙
```

## 3가지 작업

- **Ingest (수집)** — 소스를 읽고 위키 페이지들 생성/업데이트. 한 소스가 10~15개 페이지에 영향
- **Query (질의)** — 위키 기반 질의응답. 좋은 답변은 다시 위키에 저장
- **Lint (점검)** — 위키 건강 체크. 모순, 고아 페이지, 빠진 교차참조 발견

## 왜 작동하는가

> "인간이 위키를 포기하는 이유는 유지보수 부담이 가치보다 빠르게 커지기 때문이다. LLM은 지루해하지 않고, 교차참조 업데이트를 잊지 않으며, 한 번에 15개 파일을 건드릴 수 있다."

사람의 역할: 소스 큐레이션, 분석 방향 지시, 좋은 질문, 사고
LLM의 역할: 요약, 교차참조, 분류, 정리 — 그 외 모든 것

## 역사적 맥락

Vannevar Bush의 **Memex(1945)** — 연관 경로(associative trails)가 있는 개인 지식 저장소. Bush가 풀지 못한 "누가 유지보수하느냐"를 LLM이 해결한다.

## 관련 개념

- [[Zettelkasten]] — 상향식 원자적 노트 축적. LLM Wiki의 노트 성숙도 흐름과 결합
- [[PARA]] — 실행 가능성 기반 분류. LLM Wiki에서 properties로 구현
- [[Obsidian]] — LLM Wiki의 이상적인 저장소. 마크다운 기반, 그래프 뷰, git 호환
