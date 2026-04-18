---
created: 2026-04-18
tags:
  - zuora
  - accounts-receivable
  - zuora-finance
  - zuora-collections
  - 북미
  - docs
source-type: docs
author: "[[Zuora]]"
url: "https://docs.zuora.com/en/accounts-receivable/overview"
topics:
  - "[[Accounts Receivable]]"
wiki:
  - "[[Wiki/북미/Zuora/Accounts Receivable]]"
---

# Accounts Receivable — 공식 원문 (3페이지 통합)

Overview + Collections + Finance 세 페이지를 통합 수집.

---

## § Accounts Receivable overview

URL: `https://docs.zuora.com/en/accounts-receivable/overview`

Automates invoicing, collections, payments, and reporting to improve cash flow, reduce risk, and ensure financial accuracy.

The Accounts Receivable (AR) process is the backbone of managing customer payments, ensuring healthy cash flow, and mitigating financial risk. It comprises end-to-end activities from **invoice creation to payment application and risk assessment**.

Zuora provides a comprehensive set of capabilities that automate the end-to-end Accounts Receivable (AR) process. These capabilities not only streamline invoicing, collections, and payments, but also **integrate with financial reporting to function as an enterprise-level AR subledger**.

### Invoice management

- **Invoice presentation** — standard templates for billing documents + e-invoicing for digital delivery
- **Invoice operations** supported:
  - Invoice Cancellation — Cancel posted invoices
  - Invoice Deletion — Remove incorrectly created invoices
  - Invoice Reversal — Reverse invoices for corrections
  - Invoice Edits — Adjust invoice dates etc.
  - Invoice Consolidation — Combine multiple invoices into one
  - Invoice Splits — Divide single invoice into multiple
  - Invoice Write-Offs — Write off uncollectible balances

### Collections capabilities

Follow up on outstanding payments:
- Monitoring overdue balances
- Initiating customer communication
- Tracking promises to pay

### Recording payments

When customers make payments, they are recorded and reconciled against invoices. Payment recording references and integrates with existing payment capabilities.

### Invoice settlement / cash application

- Match payments to outstanding balances
- Manage payments arriving without clear invoice references
- Apply once reconciled

---

## § Zuora Collections overview

URL: `https://docs.zuora.com/en/accounts-receivable/collections`

Automate follow-ups and prioritize accounts to streamline collections.

Zuora Collections comprises:
- **Account settings** — Configure global collections settings
- **Automations** — Set collection strategy and workflow preferences
- **Dashboard reports** — Explore collections-related reports for insights and savings

Capabilities:
- Download AR aging or customer reports
- Filter-based report customization
- Manage collection activities for selected customers
- Manage collection activities for selected invoices
- Manage email and collection activities from inbox
- Password reset for temporarily blocked accounts

> Note: This feature is in the **Early Availability** phase. Access request via Zuora Global Support.

Last updated: October 10, 2025

---

## § Overview of Zuora Finance

URL: `https://docs.zuora.com/en/accounts-receivable/finance`

**Zuora Finance bridges the gap between complex recurring transactions in the subscription economy and traditional accounting.**

Zuora is built to process complex recurring transactions and designed to generate vital forward-looking subscription metrics. The general ledger on the other hand is built to process one-time transactions and designed to generate traditional income statements and balance sheets. Zuora Finance enables you to connect the complex world of recurring transactions in the subscription economy with the traditional world of accounting and finance.

### 4가지 도전과제

| # | 도전 | 설명 |
|---|------|------|
| 1 | **GL이 구독 가격 처리에 취약** | 고정/일회성 가격 모델로 만들어진 GL이 free trial, time-based promotion, varying billing frequency, usage/overage pricing 같은 복잡한 구독 가격 처리 못함 |
| 2 | **마감 프로세스가 복잡** | 구독 비즈니스는 주문당 트랜잭션이 많음. 한 주문 → 여러 bill → 더 많은 payment. 회계 마감 시 복잡성 |
| 3 | **Revenue recognition 복잡** | 구독 가격 모델이 복잡한 revenue recognition 프로세스 유발. 자동화 없으면 수작업 + audit 리스크 |
| 4 | **새로운 메트릭** | 전통 재무 메트릭 + 구독 운영 메트릭 (MRR, TCV, churn, renewal rate) 병행 필요 |

### Zuora Finance capabilities

| 기능 | 설명 |
|------|------|
| **Accounting Periods** | 회계 기간 생성 — 비즈니스의 financial period와 매칭 |
| **Chart of Accounts** | UI 또는 API로 CoA 구조 생성 — **GL과 정확히 매칭** |
| **Trial Balance** | Billing/Cash/Revenue 데이터를 언제든 요약. pending 트랜잭션 확인 |
| **Accounts Receivable Aging Reports** | 임의 회계 기간 기준 AR aging |
| **Revenue Recognition** | 자동화된 수익 인식 — earned/deferred revenue schedule 실시간 가시성 |
| **Accounting Lockdown** | 회계 기간 락업 — 마감된 기간의 트랜잭션 변경 방지 |
| **Audit Controls** | 사전 정의된 2개 role + 커스텀 role 조합. 민감 재무 데이터 접근 통제 |

### GL 통합 2가지 방식 ⭐ (SAP 연동 선택지)

| 방식 | 특징 |
|------|------|
| **Summary Integration With Journal Entries** | 월말 마감 가속화. Zuora에서 summary journal entry를 뽑아 GL에 입력. "요약 전표" |
| **Detail Integration With Connectors and Extracts** | 사전 제작된 커넥터 + 강력한 데이터 export. 상세 데이터 레벨 통합 |

> 트레이드오프: Summary = 마감 편의 / Detail = 상세 추적성 및 대사 정확도
