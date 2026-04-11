---
categories:
  - "[[Clippings]]"
tags:
  - clippings
  - knowledge-management
author:
  - "[[Andrej Karpathy]]"
url: "https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f"
created: 2026-04-12
published: 2026-04-02
topics:
  - "[[LLM Wiki]]"
  - "[[Knowledge Management]]"
domain:
  - Knowledge Management
---

## LLM Wiki — A pattern for building personal knowledge bases using LLMs

Andrej Karpathy가 제안한 LLM 기반 개인 지식 관리 패턴의 원문 요약.

### 핵심 아이디어
대부분의 LLM+문서 경험은 RAG — 파일 업로드 후 질문 시 관련 조각 검색. 매번 처음부터 지식을 재발견. **축적이 없다.**

LLM Wiki는 다르다: LLM이 **점진적으로 영구 위키를 구축하고 유지보수**한다. 새 소스가 추가되면 인덱스만 하는 게 아니라 읽고, 핵심 추출하고, 기존 위키에 통합 — 개체 페이지 업데이트, 주제 요약 수정, 모순 표시, 종합 강화.

### 아키텍처 (3층)
1. **Raw Sources** — 큐레이션된 소스 문서. 불변.
2. **Wiki** — LLM이 생성한 마크다운 파일. 요약, 개체, 개념, 비교, 종합.
3. **Schema** — CLAUDE.md 같은 규칙 문서. 구조, 규칙, 워크플로우 정의.

### 작업 (3가지)
1. **Ingest** — 소스 수집. 1소스가 10~15페이지에 영향.
2. **Query** — 위키 기반 질의. 좋은 답변은 위키에 재저장.
3. **Lint** — 건강 체크. 모순, 고아 페이지, 빠진 참조.

### 핵심 인용
> "위키는 영구적이고 복리로 성장하는 산출물이다. 교차참조는 이미 있고, 모순은 이미 표시되어 있고, 종합은 읽은 모든 것을 이미 반영한다."

> "Obsidian이 IDE이고, LLM이 프로그래머이고, 위키가 코드베이스다."

> "인간이 위키를 포기하는 이유는 유지보수 부담이 가치보다 빠르게 커지기 때문이다. LLM은 지루해하지 않는다."
