# LLM Wiki 운영 스키마

> "Obsidian이 IDE이고, LLM이 프로그래머이고, Wiki가 코드베이스다." — Andrej Karpathy

이 파일은 LLM(Claude Code)이 이 vault를 운영하는 방식을 정의한다.
**사람의 역할**: 소스 큐레이션, 질문, 사고
**LLM의 역할**: 요약, 교차참조, 분류, 정리 — 그 외 모든 것

---

## Vault 구조

```
workspace-obsidian/
├── CLAUDE.md              # 이 파일 — LLM Wiki 운영 스키마
├── Sources/             # ingest 완료된 원본 (생성 후 불변 — 수정 금지)
├── Notes/                 # 사람이 쓰는 노트, 대화 아카이브
│   └── Inbox/             # 미분류 노트 (분류 대기 중)
├── Wiki/                  # LLM이 관리하는 위키 페이지
│   ├── index.md           # 위키 전체 목차 (도메인별)
│   ├── log.md             # 활동 로그 월별 인덱스
│   └── logs/              # 월별 상세 로그
│       └── YYYY-MM.md     # 월별 활동 로그 (예: 2026-04.md)
├── Daily/                 # ingest 대기 노트 (YYYY-MM-DD_주제.md) — ingest 완료 후 삭제
├── Templates/             # 노트 템플릿 (Wiki Entity, Concept, Source 등)
└── Attachments/           # 이미지, PDF, 첨부 파일
```

### 폴더 역할 요약

| 폴더 | 누가 쓰나 | 역할 |
|------|-----------|------|
| `Sources/` | 사람+LLM | ingest 완료된 원본 (불변) — 외부 소스 및 Daily/Notes에서 Q&A 검증 후 정제된 모든 원본 |
| `Wiki/` | LLM | 컴파일된 지식 (개념, 개체, 종합) |
| `Notes/` | 사람 | 직접 쓰는 노트, 대화 아카이브 |
| `Notes/Inbox/` | 사람+LLM | 분류 대기 중인 노트 |
| `Daily/` | 사람 | Ingest 대기 스테이징 영역 — `YYYY-MM-DD_주제.md` 형식으로 작성. ingest 완료 후 삭제. 파일이 남아있으면 = 미처리 신호 |
| `Attachments/` | 사람 | 이미지, 파일 |

---

## Zettelkasten 흐름

```
Fleeting (Daily/, Notes/)  →  [Q&A Ingest]  →  Literature (Sources/)  →  Permanent (Wiki/)
    사람이 쓴다                  대화로 검증       정제된 원본 보관 (불변)       LLM이 컴파일
         ↑                                              ↑
    작업 로그, 날것의 사고                        외부 소스도 여기서 시작
```

**핵심 원칙**: Wiki의 모든 지식은 반드시 Sources를 통과한다.
자동 지식화는 없다. Daily/Notes의 내용은 Q&A ingest를 거쳐야만 Wiki로 갈 수 있다.

---

## 워크플로우

### Ingest (소스 수집)

ingest는 두 종류로 나뉜다. **자동 지식화는 없으며**, 반드시 사용자와의 Q&A 검증을 거친다.

**스킬 위치 (이 vault 안에 있음)**:
- `skills/obsidian-learn-ingest/SKILL.md`
- `skills/obsidian-ai-session-ingest/SKILL.md`
- `skills/obsidian-query/SKILL.md`

---

#### A. 문서/소스 Ingest — `obsidian-learn-ingest` 스킬 사용

대상: Daily 노트, Notes, 외부 문서, 이미지, URL 등 **존재하는 모든 소스**

```
1. 소스 분석 → 핵심 개념을 사용자에게 설명
2. Q&A로 교차검증 (사용자가 개념을 직접 확인하고 빈 곳을 채움)
3. 검증 완료 후:
   a. Sources/날짜-주제.md 생성 (정제된 원본, 이후 불변)
      - Daily / Notes/Inbox 소스: 정제된 내용 아래 원본 전문을 callout으로 포함
        `> [!quote]- 원본 노트 전문 보기`
   b. Wiki/ 관련 페이지 생성 또는 업데이트
   c. 기존 페이지의 교차참조 업데이트
   d. 원본 파일 처리:
      - **Daily/** → ingest 완료 후 **파일 삭제** (Daily = 스테이징 영역, Source에 원본 전문 포함)
      - **Notes/Inbox/** → ingest 완료 후 **파일 삭제**
      - **Notes/** (Inbox 제외) → 보존. 역참조(`related:`) 추가
4. Wiki/index.md 갱신
5. Wiki/logs/YYYY-MM.md에 항목 추가: [날짜] #ingest | 제목 (출처: daily/notes/외부)
6. Wiki/log.md 인덱스 갱신 (해당 월 항목 수 업데이트)
```

---

#### B. Claude Code 세션 대화 Ingest — `obsidian-ai-session-ingest` 스킬 사용

대상: 가치 있는 Claude Code 세션 대화 (마음에 드는 대화를 아카이브하고 싶을 때)

```
1. 대화 내용 분석 및 구조화
2. Sources/ai-sessions/YYYY-MM-DD_세션이름.md 생성 (원본, 이후 불변)
3. 관련 Wiki 페이지 생성 또는 업데이트
4. Wiki/logs/YYYY-MM.md에 항목 추가: [날짜] #archive | 대화 주제
5. Wiki/log.md 인덱스 갱신 (해당 월 항목 수 업데이트)
```

### Query (질의) — `obsidian-query` 스킬 사용

Wiki를 knowledge base 삼아 긴 호흡의 질의응답을 진행하고, 생산된 지식을 다시 Wiki로 돌려보낸다.
**Wiki가 중심이고 대화는 매개체다.**

**트리거**: "질문 시작할게", "wiki 기반으로 질문해볼게" 등

```
[Phase 1] Session Start
  - Wiki/index.md 스캔 → 관련 도메인/페이지 파악
  - 사용자에게 세션 시작 알림

[Phase 2] Q&A Loop
  - 질문마다 관련 wiki 페이지를 on-demand로 읽어 답변
  - wiki에 없는 내용은 명확히 표시
  - 5회마다 "계속 이어갈까요?" 상기 알림
  - 종료 신호 감지: "충분해", "이해했어", "마무리할게" 등

[Phase 3] Query-Ingest
  1. Sources/ai-queries/YYYY-MM-DD_주제.md 생성 (Q&A 원문)
  2. Wiki 페이지 생성(신규) 또는 보강(기존)
  3. Sources ↔ Wiki 양방향 링크 연결
  4. Wiki/logs/YYYY-MM.md에 항목 추가: [날짜] #query | 주제
  5. git commit + Obsidian 딥링크 제공
```

### Lint (건강 체크)

"위키 정리해줘" 또는 정기 점검 시:

```
1. 페이지 간 모순 발견 및 표시
2. 고아 페이지 탐색 (인바운드 링크 없는 페이지)
3. 빠진 교차참조 보완
4. 구식 정보 표시
5. Wiki/logs/YYYY-MM.md에 항목 추가: [날짜] #lint | 요약
```

---

## Source 형식

Source 파일은 내용이 불변이다. 단, **wiki 작성 완료 후 `wiki:` 프로퍼티 추가는 예외**로 허용한다.

```markdown
---
created: YYYY-MM-DD
tags: [...]
source-type: daily | notes | 외부 | docs | ai-session | ai-query
wiki:                                  ← Wiki 작성 완료 후 추가 (내용 아님, 링크만)
  - "[[Wiki/도메인/페이지명]]"
---
```

`wiki:` 없으면 = 아직 Wiki로 승격되지 않은 Source.

---

## Wiki 페이지 형식

```markdown
---
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [도메인, 개념]
sources:                               ← 이 Wiki를 생성한 Source 목록 (양방향 링크)
  - "[[Sources/원본파일]]"
---

# 페이지 제목

한 줄 요약.

## 개요
...

## 관련 개념
- [[다른 위키 페이지]]

## 참고 소스
- [[Sources/원본]]
```

---

## Callout 스타일 규칙

Wiki 페이지에서 텍스트 아트(아스키 다이어그램, 코드처럼 보이는 흐름)는 **Obsidian Callout** 으로 표현한다.

```markdown
> [!example] 제목 (선택)
> 내용
```

| 용도 | Callout 타입 |
|------|------------|
| 흐름/다이어그램/예시 | `[!example]` |
| 중요 제약/주의사항 | `[!important]` |
| 팁/힌트 | `[!tip]` |
| 정보 | `[!info]` |

> **원칙**: 코드 블록(` ``` `)은 실제 코드(Kotlin/SQL/JSON)에만 사용. 흐름 도식은 [!example] Callout.

---

## 명명 규칙

- **Wiki 페이지**: `개념명.md` (한국어 또는 영어, 일관성 유지)
- **Daily 노트**: `YYYY-MM-DD_주제.md` — ingest 대기 파일. 처리 완료 후 삭제
- **Sources**: 출처 유형에 따라 구분
  - 외부 소스 (일반/Figma): `외부-YYYY-MM-DD-제목.md`
  - 코드 분석 (Figma 없이 코드만): `코드분석-YYYY-MM-DD-주제.md`
  - GitHub 레포 / 기술 URL: `github/YYYY-MM-DD_레포명.md`
  - 공식 문서 / 레퍼런스: `docs/[도구명]/YYYY-MM-DD_페이지제목.md`
  - Daily에서 ingest: `daily/YYYY-MM-DD_제목.md`
  - Notes에서 ingest: `notes-YYYY-MM-DD-제목.md`
  - 세션 대화 아카이브: `ai-sessions/YYYY-MM-DD_세션이름.md`
  - AI 질의응답: `ai-queries/YYYY-MM-DD_주제.md`
  - 운영 이슈: `운영이슈-YYYY-MM-DD-주제.md`
- **Notes**: 자유 형식

---

## 도메인 현황 (2026-04-18 기준)

이 vault는 현대자동차그룹 빌링/정산 시스템 개발 업무를 주로 다룬다:

| 도메인 | Wiki 위치 | 내용 |
|--------|----------|------|
| **HMG-Settlement** | `Wiki/HMG-Settlement/` | 정산 시스템 핵심 (60개 엔티티, 전표·반제·결산 흐름) |
| **북미** | `Wiki/북미/` | HMA(현대 미국)/KUS(기아 미국), Zuora(구독관리), 반제 전략 |
| **배경지식** | `Wiki/배경지식/` | 기술(SAP·회계·DB·Kotlin), 인물, 도구, 비즈니스 |
| **인프라** | `Wiki/인프라/` | k8s, ArgoCD, Harbor, hCloud, GitLab CI, AIP Gateway |
| **Claude Code** | `Wiki/Claude Code/` | Claude Code 사용법, 서브에이전트, 스킬, 공식 문서 |
| **HMG-Series** | `Wiki/HMG-Series/` | hmg-notification 등 공통 서비스 |
| **Database** | `Wiki/배경지식/Tech/Database/` | 동시성 락, 데드락, 트랜잭션 전파 |

---

## 도메인 라우팅 규칙

### SAP 도메인

| 내용 유형 | Wiki 위치 |
|----------|----------|
| SAP 시스템, 전표유형, 인터페이스 등 SAP 전반 | `Wiki/배경지식/Tech/SAP/` |
| 회계 용어 (복식부기, 차대변, GL, 전기키 등) | `Wiki/배경지식/Tech/SAP/Accounting/` |
| HMG-Settlement의 SAP 연동 구현 | `Wiki/HMG-Settlement/` (기존 유지) |

> SAP 도메인은 **도메인 지식**만 담는다. HMG-Settlement의 SAP 엔티티(sap-slip, sap-deposit-slip 등)는 기존 위치를 유지한다.

### 배경지식 도메인

| 내용 유형 | Wiki 위치 |
|----------|----------|
| 기술 개념 (언어, DB, 프레임워크, SAP 등) | `Wiki/배경지식/Tech/` |
| 인물 (Karpathy, Steph Ango 등) | `Wiki/배경지식/People/` |
| 도구 (Obsidian, Claude Code 등) | `Wiki/배경지식/Tool/` |
| 비즈니스 개념 (카드 결제 생태계 등) | `Wiki/배경지식/Business/` |
