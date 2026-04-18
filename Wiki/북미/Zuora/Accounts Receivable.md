---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - accounts-receivable
  - zuora-finance
  - zuora-collections
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_accounts-receivable]]"
---

# Accounts Receivable (Zuora)

**AR 전 과정을 자동화하는 제품군.** Invoice 생성 → Collections → Payment 기록 → Invoice settlement(cash application) → GL 통합까지. 자체 포지셔닝은 **"enterprise-level AR subledger"**.

## 본인 프로젝트(SAP 연동)에서 가장 중요한 제품 ⭐⭐

> [!important] 왜 AR이 본인 작업에 가장 직결되나
> Zuora AR은 **"구독 경제의 복잡한 recurring 트랜잭션을 전통 회계(GL)로 연결"**하는 다리. 이 다리가 바로 본인이 만들어야 하는 것.
> - Zuora Finance가 **GL과의 통합 레이어** 제공
> - **Chart of Accounts를 Zuora에서 관리해 SAP과 매칭** 가능
> - **Accounting Periods / Lockdown**이 SAP 마감과 정렬
> - **Journal Entries 추출**이 SAP 전표 생성의 원천이 될 수 있음

## AR 4단계 파이프라인 (정산 전표의 원형)

```
① Invoice management → ② Collections → ③ Recording payments → ④ Invoice settlement / cash application
```

이 순서가 본인이 만들 SAP 정산 파이프라인의 **논리적 템플릿**.

### ① Invoice Management — 인보이스 운영 7종

| 동작 | 의미 |
|------|------|
| Invoice Cancellation | Posted 인보이스 취소 |
| Invoice Deletion | 잘못 만든 인보이스 삭제 |
| Invoice Reversal | 정정을 위한 역전표 |
| Invoice Edits | 날짜 등 필드 수정 |
| Invoice Consolidation | 여러 인보이스 통합 |
| Invoice Splits | 한 인보이스 분리 |
| Invoice Write-Offs | 회수 불가 잔액 상각 |

> SAP 연동 관점: 각 동작마다 **SAP 측 역전표/수정 전표** 매칭 로직 필요.

### ② Collections — 미수금 관리 (Early Availability)

Zuora Collections 구성:
- **Account settings** — 글로벌 collections 설정
- **Automations** — 전략/워크플로우
- **Dashboard reports** — AR aging, customer report

⚠️ **Early Availability 단계** — 완전 GA 아님. 접근 요청을 Zuora Global Support로 해야 함. HMG 북미가 이 기능을 쓰는지 회사 PC에서 확인 필요.

### ③ Recording Payments — 결제 기록 + Reconciliation

고객이 낸 돈을 기록하고 인보이스와 reconcile. [[Zuora Payments]]의 결제 처리 능력과 연계.

### ④ Invoice Settlement / Cash Application — 매칭 ⭐ 반제의 핵심

- Payment ↔ Invoice 매칭
- **Invoice reference 없이 도착한 Payment** 관리 (unapplied payment)
- Reconcile 완료 후 적용

> [!tip] 반제 전략의 본체
> 사용자가 초기 요청에서 언급한 "반제 전략"은 이 단계가 본체. Unapplied Payment 처리, 부분 매칭, [[Credit Memo]] 우선순위 등이 여기서 결정됨.

## Zuora Finance — GL 연결 레이어

> "Zuora Finance bridges the gap between complex recurring transactions in the subscription economy and traditional accounting."

### 4대 도전과제 (공식 명시)

| # | 도전 | 본인 작업 관련성 |
|---|------|----------------|
| 1 | GL이 구독 가격 처리에 취약 (free trial, usage, overage) | ⭐ SAP 전표 생성 로직에서 직면 |
| 2 | 마감 프로세스 복잡 (order 1개 → bill 다수 → payment 다수) | ⭐ 월말 정산 배치 설계 |
| 3 | Revenue recognition 복잡 | △ 별도 Zuora Revenue 제품 영역 |
| 4 | 새로운 메트릭 (MRR, TCV, churn, renewal rate) | 🟡 운영 지표 |

### 핵심 기능 7개

| 기능 | 본인 작업 의미 |
|------|---------|
| **Accounting Periods** | SAP 회계기간과 매칭 |
| **Chart of Accounts** | ⭐⭐ SAP CoA와 정렬 — **전표 계정 매핑의 본체** |
| **Trial Balance** | 대사 검증 |
| **AR Aging Reports** | 미수금 현황 (반제 전략 근거) |
| **Revenue Recognition** | ASC 606 — (주로 Zuora Revenue로) |
| **Accounting Lockdown** | SAP 마감 동기화 |
| **Audit Controls** | 감사 대응 |

## GL(=SAP) 통합 2가지 방식 ⭐⭐⭐

공식 문서가 제시하는 두 선택지:

| | Summary Integration With Journal Entries | Detail Integration With Connectors and Extracts |
|---|---|---|
| **방식** | Zuora에서 summary journal entry 추출 → GL 입력 | 사전 제작 커넥터 + 데이터 export로 상세 레벨 통합 |
| **이점** | 월말 마감 가속 | 상세 추적성, 정확한 대사 |
| **단점** | 개별 트랜잭션 소실 | 마감 복잡도 유지 |
| **적합** | 트랜잭션 볼륨 보통, 마감 간소화 우선 | 고볼륨, 감사·대사 엄격 |

> [!important] HMG 북미의 선택
> 어느 방식을 쓰는지(또는 둘 혼합인지)는 **회사 내부 결정**. 개인 vault에서는 두 방식이 존재한다는 사실만 기록. 실제 선택/구현은 회사 PC vault로.

## 본인 프로젝트(4 Documents → SAP)에 대한 최종 뷰

4 Documents가 여기에 어떻게 매핑되는지:

| Document | AR 파이프라인 위치 | Finance 관점 |
|----------|--------------|---------|
| [[Invoice]] | ① Invoice management | CoA에 따라 매출/AR 계정 |
| [[Debit Memo]] | ① (추가 invoice로 취급) | AR 증가 |
| [[Credit Memo]] | ④ (settlement의 대안) | AR 감소 (매출 차감) |
| [[Payment]] | ③ + ④ | 현금 → AR 감소 |

→ 4 Documents는 각각 **Journal Entry로 변환**되어 SAP에 전달됨.

## 관련

- [[Zuora Billing]] — Invoice/CM/DM 생성
- [[Zuora Payments]] — Payment 생성
- [[Zuora Platform]] — Integration 경로 제공
- [[Invoice]] · [[Payment]] · [[Credit Memo]] · [[Debit Memo]]
- [[Subscription]] — Invoice의 상위 맥락
- **Invoice Settlement** — 반제 기능 세트 (추후 ingest)
- **Journal Entry** — GL 연동 단위 (추후 ingest)
- **Chart of Accounts** — CoA 매핑 (추후 ingest)

## 참고 소스

- [[../../../Sources/docs/zuora/2026-04-18_accounts-receivable|Sources/docs/zuora/2026-04-18_accounts-receivable]]
- 공식: [Accounts Receivable overview](https://docs.zuora.com/en/accounts-receivable/overview) · [Collections](https://docs.zuora.com/en/accounts-receivable/collections) · [Finance](https://docs.zuora.com/en/accounts-receivable/finance)
