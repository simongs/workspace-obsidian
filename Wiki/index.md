---
tags:
  - wiki
  - index
updated: 2026-04-18

---

# Wiki Index

LLM(Claude Code)이 ingest/query/lint 실행 시 자동으로 갱신한다. 도메인별로 정리된 Wiki 전체 목차.

> 운영 규칙: [[../CLAUDE|CLAUDE.md]]
> 활동 로그: [[log|log.md]]

---

## 도메인

### HMG-Settlement — 정산 시스템 (주력 도메인)

현대자동차그룹 빌링/정산 시스템. 60+ 엔티티, 전표·반제·결산 흐름.

- 위치: `Wiki/HMG-Settlement/`
- 아직 개인 vault에는 없음(회사 PC 전용)

### 북미 — HMA / KUS / Zuora

- 위치: `Wiki/북미/`
- [[북미/Zuora/Zuora Billing|Zuora Billing]] — 구독/청구 플랫폼, Invoice·Credit Memo·Debit Memo 생성
- [[북미/Zuora/Zuora Payments|Zuora Payments]] — PG 오케스트레이션, Payment·Refund 생성
- **4 Documents** (Zuora → SAP 연동 대상):
    - [[북미/Zuora/documents/Invoice|Invoice]] — 정상 청구 (AR ↑)
    - [[북미/Zuora/documents/Payment|Payment]] — 결제 수령 (AR ↓)
    - [[북미/Zuora/documents/Credit Memo|Credit Memo]] — 잔액 차감·환급성 (AR ↓)
    - [[북미/Zuora/documents/Debit Memo|Debit Memo]] — 추가 청구 (AR ↑)

### HMG-Series — 공통 서비스

- 위치: `Wiki/HMG-Series/`

### 인프라

- 위치: `Wiki/인프라/`

### Claude Code — 사용법, 스킬, 공식 문서

- 위치: `Wiki/Claude Code/`
- [[Claude Code/claude-design|Claude Design]] — Anthropic Labs의 대화형 디자인 생성 도구 (research preview)

### 배경지식

기술 / 인물 / 도구 / 비즈니스 개념. 다른 도메인의 기반이 되는 공통 지식.

#### Tech

**Kotlin** (`배경지식/Tech/Kotlin/`)
- [[Kotlin]] — 언어 전체 개요, 특징, 사용 영역
- [[Kotlin 변수와 타입]] — val/var, 타입 추론, 스마트 캐스트
- [[Kotlin 함수]] — fun 키워드, 블록/표현식 본문, Unit
- [[Kotlin 제어 흐름]] — if/when 표현식, for, while, ranges
- [[Kotlin 컬렉션]] — List/Set/Map, filter/map/sortedBy
- [[Null Safety]] — nullable 타입(?), null 체크

#### People (`배경지식/People/`)

- [[Andrej Karpathy]] — LLM Wiki 패턴 제안자
- [[Steph Ango]] — Obsidian CEO, vault 구조 원칙

#### Tool (`배경지식/Tool/`)

- [[Obsidian]] — 마크다운 기반 지식 관리 도구
- [[Obsidian Bases]] — frontmatter 기반 동적 뷰
- [[JetBrains]] — Kotlin 개발사

#### 배경지식 루트 (공통 개념)

- [[LLM Wiki]] — LLM이 위키를 자동 관리하는 지식 시스템
- [[Zettelkasten]] — 상향식 원자적 노트 축적 방법론
- [[PARA]] — 실행 가능성 기반 정보 분류 방법론

#### English (`배경지식/English/`)

- [[English Grammar]] — 영어 문법 전체 체계
- [[현재완료]] — have + p.p.

#### Business

- (준비 중)

---

## 미확장 주제 (Wiki 대기열)

ingest 되면 위로 승격된다.

### Kotlin
- 코루틴 — 비동기 프로그래밍
- 확장 함수 — 기존 클래스에 함수 추가
- 데이터 클래스 — equals/hashCode/copy 자동 생성
- sealed 클래스 — 제한된 클래스 계층
- Kotlin DSL

### English
- 과거 시제 — 현재완료와 비교되는 기본 시제
- 과거분사 (p.p.)
- 현재완료진행 — have been + V-ing
