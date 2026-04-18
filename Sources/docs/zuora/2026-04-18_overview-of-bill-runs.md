---
created: 2026-04-18
tags:
  - zuora
  - bill-run
  - zuora-billing
  - 북미
  - docs
source-type: docs
author: "[[Zuora]]"
url: "https://docs.zuora.com/en/zuora-billing/bill-your-customer/bill-runs/automate-billing-document-generation/overview-of-bill-runs"
topics:
  - "[[Bill Run]]"
wiki:
  - "[[Wiki/북미/Zuora/flows/Invoice Payment Flow]]"
---

# Overview of bill runs — 공식 원문

> 2026-04-18 수집

## 공식 본문 (요약 인용)

### Overview

Bill runs automate the creation of invoices and credit memos for customer accounts, supporting both scheduled and ad hoc billing processes. A bill run **gathers information from one or more customer accounts and generates invoices for those accounts**. Also referred to as **invoice runs**.

**Invoice Settlement** 기능이 켜져 있으면 bill run이 negative charge에 대해 **Credit Memo 자동 생성**. 생성된 invoice/credit memo 수는 bill run 개별 페이지에 표시.

Each bill run can be for:
- a single customer
- a group of customers (batch, 또는 같은 bill cycle day)
- all customers

Bill runs can be scheduled in advance or run ad hoc.

> "After invoices have been created in the bill run, they are now ready to collect for payments. This is where **payment runs** come in: they are used to collect cash from your customers."

### Types of bill runs

- **Ad hoc bill run** — 수동 on-demand 실행
- **Scheduled (recurring) bill run** — 정기 (매주/매월 등)

> "Invoices must be **posted** before you can collect payments."

### Billing in advance

인보이스 date와 target date를 다르게 설정해 **선불 청구 가능**. 예: invoice date 3/1, target date 4/1~4/30 → 3/1에 4월분 인보이스 발송.

### Calculated charges

Normal charges 먼저, 그 다음 calculated charges. 부모/자식 계층 계산은 eligible charge 모두 청구된 뒤로 deferred.

### Considerations

- 1 invoice 최대 **20,000 charge**
- **Filter Charges**: One time / Recurring / Usage 타입별로 포함/제외 가능 — **일회성 vs 구독 bill run 분리 가능**
- 같은 account에 동시 bill run 금지 (conflict 위험)

### Status

Bill run status: Posted → Completed (invoice가 Posted → Draft로 역행하면 bill run도 Completed로 변경)
