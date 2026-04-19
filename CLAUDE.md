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
├── Sources/               # ingest 완료된 원본 (생성 후 불변 — 수정 금지). LLM Wiki의 유일한 입력
├── Wiki/                  # LLM이 관리하는 위키 페이지 (Sources로부터 컴파일)
│   ├── index.md           # 위키 전체 목차 (도메인별)
│   ├── log.md             # 활동 로그 월별 인덱스
│   └── logs/              # 월별 상세 로그
│       └── YYYY-MM.md     # 월별 활동 로그 (예: 2026-04.md)
├── Notes/                 # 사용자 개인 메모 공간 (LLM Wiki 입력 아님)
├── Daily/                 # ingest 대기 노트 (YYYY-MM-DD_주제.md) — ingest 완료 후 삭제
├── Templates/             # 노트 템플릿
└── Attachments/           # 이미지, PDF, 첨부 파일
```

### 폴더 역할 요약

| 폴더 | 누가 쓰나 | 역할 |
|------|-----------|------|
| `Sources/` | 사람+LLM | **LLM Wiki의 유일한 입력**. Q&A 검증을 거친 불변 원본. Tier 1(공식 문서/Reference) + Tier 2(저자 아티클) |
| `Wiki/` | LLM | 컴파일된 지식 (개념, 개체, 종합). Sources로부터만 생성 |
| `Notes/` | 사람 | **정제 의도 없는 사용자 개인 메모 공간**. vault 메타 원칙·회고·evergreen. 영원히 가변. **LLM은 Wiki 컴파일 입력으로 쓰지 않음**. 정제해서 영구화할 판단이 서면 사용자 명시 지시로 Sources로 승격 |
| `Daily/` | 사람 | Ingest 대기 스테이징 영역 — `YYYY-MM-DD_주제.md` 형식으로 작성. ingest 완료 후 삭제. 파일이 남아있으면 = 미처리 신호 |
| `Attachments/` | 사람 | 이미지, 파일 |

---

## LLM Wiki 입력 원칙

```
Daily/ (수집) ──▶ [Q&A Ingest] ──▶ Sources/ (불변 원본) ──▶ Wiki/ (LLM 컴파일)

Notes/ (사용자 개인 메모)  — LLM Wiki 입력 아님. 필요시 명시 승격으로 Sources 이동.
```

**핵심 원칙**:
- **Wiki의 모든 지식은 반드시 Sources를 통과한다.** 자동 지식화 없음.
- **LLM Wiki 컴파일 입력은 Sources/ 단 하나.** Notes/는 사용자 개인 공간이며 Wiki 생성 대상에서 제외.
- Notes/의 내용이 Wiki로 가려면 **사용자 명시 지시**로 Sources에 승격 후 ingest를 거친다.

### Source의 자격 기준

Source = **"정제 의도를 갖고 Q&A 검증을 거쳐 불변으로 고정된 기록"**.

외부/내부가 자격을 결정하지 않는다. **정제 상태**가 결정한다.

| 상태 | 위치 | 성격 |
|------|------|------|
| 정제 의도 없음, 가변 | `Notes/` | 개인 메모, 평생 수정 가능, LLM 미사용 |
| 정제 중 (스테이징) | `Daily/` | 초벌 정리, ingest 대기 |
| **정제 완료, 불변** | `Sources/` | **이 상태가 Source 자격** |
| 컴파일된 지식 | `Wiki/` | Sources로부터 LLM이 생성 |

즉 **"내가 쓴 것도 Source가 될 수 있다"**. Q&A 검증이라는 관문을 통과했다면. 반대로 **정제할 의도가 없는 내 생각**(vault 운영 원칙, 회고 등)은 Source가 아닌 `Notes/`에 머문다.

### Source 자격 Tier (정제된 Source의 유형 구분)

| Tier | 저자 | 내용 | source-type | Wiki 반영 |
|------|------|------|-----------|-----------|
| **Tier 1** | 외부 | 공식 문서, 기술 스펙, Reference | `docs` | 사실로 컴파일, 저자 귀속 불필요 |
| **Tier 2** | 외부 | 저자 주장 아티클 (블로그·에세이·오피니언) | `article` | **저자 귀속 인용 필수** — "X는 ~라 주장한다" |
| **Tier 3** | 사용자+LLM | Claude Code 세션, Q&A 기록 | `ai-session`, `ai-query` | 사건 기록으로 참조 |
| **Tier 4** | 사용자 | Daily에서 정제된 학습 노트 / Article 소감 / 검증된 정리물 | `daily`, `notes`, `article`(소감 부분) | 사용자의 정제된 기록으로 반영. 외부 근거 있으면 함께 인용 |

> **Tier 2 주의**: Wiki에 **사실처럼** 서술 금지. 반드시 저자 귀속 인용 형태.
> **Tier 4 주의**: Article 소감은 원문(Tier 2)과 같은 Source 파일 안에 공존하되, Wiki 반영 시 "저자 주장"과 "내 해석"을 명확히 분리.

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
1. 소스 분석 → 1차 정리 제시
2. 리뷰 & 수정 반영 (Q&A는 필요 시)
3. 검증 완료 후:
   a. Sources/날짜-주제.md 생성 (정제된 원본, 이후 불변)
      - Daily 소스: 정제된 내용 아래 원본 전문을 callout으로 포함
        `> [!quote]- 원본 노트 전문 보기`
   b. Wiki/ 관련 페이지 생성 또는 업데이트
   c. 기존 페이지의 교차참조 업데이트
   d. 원본 파일 처리:
      - **Daily/** → ingest 완료 후 **파일 삭제** (Daily = 스테이징 영역, Source에 원본 전문 포함)
      - **Notes/** → LLM은 수정하지 않음 (사용자 개인 공간)
4. Wiki/index.md 갱신
5. Wiki/logs/YYYY-MM.md에 항목 추가: [날짜] #ingest | 제목 (출처: daily/외부/article)
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
