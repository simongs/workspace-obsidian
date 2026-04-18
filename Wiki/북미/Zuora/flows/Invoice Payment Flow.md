---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - flow
  - invoice
  - payment
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_get-started-with-subscriptions]]"
  - "[[Sources/docs/zuora/2026-04-18_overview-of-bill-runs]]"
  - "[[Sources/docs/zuora/2026-04-18_invoice-management]]"
  - "[[Sources/docs/zuora/2026-04-18_process-payments]]"
  - "[[Sources/docs/zuora/2026-04-18_accounts-receivable]]"
---

# Invoice ↔ Payment Flow — 일회성 vs 월 구독

**사용자가 처음 물었던 "일회성 결제와 월 구독 결제의 인보이스/페이먼트 흐름"** 비교. 기존 Source들에서 종합.

## 핵심 차이 한 줄 요약

| | 원천 | Invoice 생성 | Payment 수집 |
|---|---|---|---|
| **월 구독** | [[Subscription]] (Rate Plan Charge) | **Bill Run** (scheduled/recurring) | **Payment Run** (scheduled) |
| **일회성** | Order Line Item (non-subscription) | **Bill Run** (ad hoc) 또는 수동 post | Ad hoc payment 또는 API |

둘 다 최종적으로 **동일한 [[Invoice]] Document**로 발행되지만, **트리거와 배치 단위가 다르다**.

## 플로우 A — 월 구독 결제 (Recurring)

### 전체 흐름

> [!example] 월 구독 라이프사이클
> ```
> [Product Catalog]
>       │
>       ▼
> [Subscription 생성]  ──── Termed(연/월) or Evergreen
>       │                   Subscription Date, Bill Cycle Date 확정
>       ▼
> [Rate Plan Charge]  ──── 각 charge의 Next Charge Date, ChargedThroughDate
>       │
>       ▼
> [Scheduled Bill Run]  ─── 매월 Bill Cycle Day 기준 자동 실행
>       │                   여러 account 일괄 처리 (배치)
>       ▼
> [Invoice 생성(Draft)]
>       │
>       ▼
> [Invoice Post]        ─── Post되어야 Payment 수집 가능
>       │
>       ▼
> [Scheduled Payment Run] ── 결제 수단에 자동 청구
>       │                    (retry·dunning·PG 오케스트레이션)
>       ▼
> [Payment 레코드 생성]
>       │
>       ▼
> [Cash Application]     ── Payment ↔ Invoice 매칭
>       │
>       ▼
> [AR 감소 + 회계 전표]  ── GL(SAP)로 연동
> ```

### 변형 케이스

| 상황 | 처리 |
|------|------|
| 구독 변경 (upgrade/downgrade) | **Amendment** → 새 Subscription Version → 기존 version은 종료 처리 |
| 기간 중 해지 | Credit Memo(proration credit)로 미사용분 환급 |
| 결제 실패 | **Dunning** 프로세스 (자동 재시도, 이메일 알림) |
| 인보이스 오류 | **Credit Memo** (과다청구) 또는 **Debit Memo** (과소청구) |
| 인보이스 자체 취소 | Invoice Cancellation / Reversal / Write-off |

### 특징

- **주기적**. 매 Bill Cycle마다 반복
- **배치**. Payment Run이 수많은 구독을 한 번에 결제
- **Unapplied Payment** 많이 발생 가능 (구독 갱신 결제가 어느 invoice에 갈지 자동 매칭)
- **예측 가능** — 다음 청구/결제 예정일 계산 가능 (Rate Plan Charge level)

## 플로우 B — 일회성 결제 (One-time)

### 전체 흐름

> [!example] 일회성 결제 라이프사이클
> ```
> [Product or Service 선택]
>       │
>       ▼
> [Order Line Item 생성]  ── Subscription 없이 독립
>       │                    또는 기존 Subscription과 연계
>       ▼
> [Ad hoc Bill Run 또는 수동 Invoice 생성]
>       │                    (Filter Charges → "One time"만)
>       ▼
> [Invoice 생성(Draft)]
>       │
>       ▼
> [Invoice Post]
>       │
>       ▼
> [수동/API Payment]       ── Payment Form·Payment Page·API 등
>       │                    Payment Run이 아닌 개별 처리가 주
>       ▼
> [Payment 레코드 생성]
>       │
>       ▼
> [Cash Application]
>       │
>       ▼
> [AR 감소 + 회계 전표]
> ```

### 변형 케이스

| 상황 | 처리 |
|------|------|
| 환불 요청 | **Credit Memo** 발행 → **Refund** (현금 환불) |
| 반품 | Credit Memo (품목 단위) |
| 잘못된 청구 | Invoice Cancellation 또는 Debit/Credit Memo |
| 세금만 별도 조정 | Credit/Debit Memo에서 tax item 조정 |

### 특징

- **단발성**. 반복 없음
- **개별 처리**. Payment Run 대신 즉시 결제가 주
- **예측 불가** — 발생 시점이 사용자 액션에 의존
- **Order Line Item**이라는 **비구독 트랜잭션** 단위 (Zuora 공식 용어)

## 공통 단계 — Bill Run & Payment Run

두 플로우 모두 **동일한 기반**을 사용:

| 기반 | 역할 |
|------|------|
| **Bill Run** | Invoice(+Credit Memo) 자동 생성. Ad hoc 또는 Scheduled. **Filter Charges로 One time / Recurring / Usage 선택 가능** |
| **Invoice Post** | Posted 상태로 전환. **Post 없으면 Payment 수집 불가** |
| **Payment Run** | 결제 일괄 수집 (주로 구독용). 일회성은 개별 처리 |
| **Invoice Settlement** | Payment/Credit Memo를 Invoice에 apply. 반제 |

## 4 Documents의 발생 시점

플로우별 4 Documents 발생 패턴:

| Document | 월 구독에서 | 일회성에서 |
|----------|----------|---------|
| **Invoice** | 매 Bill Cycle마다 자동 | 개별 Order Line Item 발생 시 |
| **Payment** | Payment Run 배치 | 개별 Payment Form/API |
| **Credit Memo** | Proration 취소, 이벤트 driven | 환불·반품·오류 정정 |
| **Debit Memo** | Undercharge 사후 정정 | Ad hoc 추가 청구 |

## SAP 연동 관점 — 두 플로우를 어떻게 다루나

> [!important] 두 플로우의 SAP 연동 패턴이 다를 수 있다
> - **월 구독**: 예측 가능 + 배치 중심 → **Scheduled 배치 연동** 또는 **이벤트 기반 webhook** 둘 다 적합
> - **일회성**: 비정기 + 개별 처리 → **실시간 webhook** 이 더 적합 (배치면 지연)
>
> 혼합 운영 시 하나의 연동 파이프라인에 두 성격이 섞여 들어옴. Document 타입/속성으로 구분해 SAP 전표 생성 로직 분기 가능.

## 아직 파내지 않은 관련 개념

세션 B 보강 시 다룰 주제:
- **Payment Run** 공식 페이지 상세
- **Amendment** (구독 변경) 라이프사이클
- **Invoice Settlement** 세부 (apply/unapply 규칙)
- **Order Line Item** 공식 정의
- **Dunning** 프로세스

## 관련

- [[Invoice]] · [[Payment]] · [[Credit Memo]] · [[Debit Memo]]
- [[Subscription]] — 월 구독의 원천
- [[Zuora Billing]] — Bill Run
- [[Zuora Payments]] — Payment Run
- [[Accounts Receivable]] — AR 파이프라인 (이 플로우의 상위 맥락)

## 참고 소스

- [[../../../../Sources/docs/zuora/2026-04-18_get-started-with-subscriptions|get-started-with-subscriptions]]
- [[../../../../Sources/docs/zuora/2026-04-18_overview-of-bill-runs|overview-of-bill-runs]] ⭐ (이번에 신규)
- [[../../../../Sources/docs/zuora/2026-04-18_invoice-management|invoice-management]]
- [[../../../../Sources/docs/zuora/2026-04-18_process-payments|process-payments]]
- [[../../../../Sources/docs/zuora/2026-04-18_accounts-receivable|accounts-receivable]]
