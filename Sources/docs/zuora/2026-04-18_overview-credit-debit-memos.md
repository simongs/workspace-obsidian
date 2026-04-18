---
created: 2026-04-18
tags:
  - zuora
  - credit-memo
  - debit-memo
  - invoice-settlement
  - zuora-billing
  - 북미
  - docs
source-type: docs
author: "[[Zuora]]"
url: "https://docs.zuora.com/en/zuora-billing/bill-your-customer/adjust-invoice-amounts/invoice-settlement/credit-memos-and-debit-memos/overview-of-credit-memos-and-debit-memos"
topics:
  - "[[Credit Memo]]"
  - "[[Debit Memo]]"
wiki:
  - "[[Wiki/북미/Zuora/documents/Credit Memo]]"
  - "[[Wiki/북미/Zuora/documents/Debit Memo]]"
---

# Overview of credit memos and debit memos — 공식 원문

> 2026-04-18 수집. Invoice Settlement 기능이 켜져 있어야 사용 가능.

## 공식 본문

## Overview of credit memos and debit memos

With the Invoice Settlement feature enabled, you can use Zuora Billing to generate credit memos and debit memos when necessary.

Like invoices, credit and debit memos are legal documents vendors issue to their customers. They are used to correct charge mistakes on invoices and to manage the balance due on a particular invoice or account.

- **Credit memos reduce invoice and account balances.** By applying one or more credit memos to invoices with positive balances, you can reduce the invoice balances in the same way that you apply a payment to an invoice.
- **Debit memos increase the amount a customer owes.** It is a separate document from the invoice. Debit memos can be used to correct undercharging on an invoice or to levy ad hoc charges outside the context of a subscription. Just like an invoice, debit memo balances can be settled by applying either a payment or a credit memo.

## Purposes

Credit and debit memos provide a detailed justification of the amount stated on the memo. You can use the memos to adjust invoices at a line item level and provide your customers with memo documents to track billing changes.

You might need to issue credit and debit memos under any of the following situations:

- Correct invoice errors, such as overcharging, undercharging, and other billing mistakes
- Credit an account without refunding payment
- Issue ad hoc charges or discounts
- Write off account balances, such as reducing an invoice balance to zero due to bad debt.
- Reflect negative charges
- Debit an over-delivery for your customer

With the Invoice Settlement feature enabled, the credit and debit memos can be issued in the following ways:

- **Refer to specific invoices** — 특정 인보이스의 항목 조정. memo items이 invoice items를 참조.
- **Standalone** — 특정 인보이스와 무관한 ad hoc charge/credit. memo items이 one-time product rate plan charges 참조. 양수 잔액이면 임의 invoice/debit memo에 적용 가능.
- **Generate credit memos by bill run** — 부 invoice 생성 대신 bill run이 negative charge 이벤트를 credit memo로 자동 생성. AR 반제 관리에 유리.

You can find the memos list by navigating to Billing > Credit and Debit Memos in the left navigation from the Zuora UI.

## Key features

In many countries, credit and debit memos are actually required by law for adjusting invoices. These regulations are particularly widespread in Europe. Credit and debit memos are a widely adopted and well-understood billing construct that makes it easier to audit a company's billing and verify the correctness of receivables accounting.

After the Invoice Settlement feature is enabled, it fully replaces:
- Invoice Adjustments
- Invoice Item Adjustments
- Credit Balance Adjustments

Capabilities:
- Create credit and debit memos from invoices or from one-time charges
- Apply credit memos to invoices and debit memos to settle outstanding balances
- Unapply/reapply memos to other invoices and debit memos
- **Refund a certain amount of the credit memo balance to your customer** ← Refund는 Credit Memo의 후속 액션
- Calculate taxes (Zuora Tax, Avalara Integration, 3rd-party)
- Adjust tax items on invoices
- Customize memo templates (PDF 생성)
- Reverse invoice / write off invoice
- Callout/email notifications on memo create/post
- Preview memos via REST API
- Specify auto-apply behavior during payment runs
- Apply/unapply memos in closed accounting periods
- Finance integration with Invoice Settlement

## Use cases

- Correct errors (wrong price, disabled proration, late cancel)
- Undercharges → **Debit memo**로 잔액 증가
- Overcharges → **Credit memo**로 잔액 감소
- Proration credit (선불 연간 서비스의 미사용분 환급)
- Charge disputes
- Discounts (기간 한정 할인)
- Ad hoc charges or credits (고객 loyalty credit 등)
