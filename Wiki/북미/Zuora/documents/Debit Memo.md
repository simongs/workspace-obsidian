---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - debit-memo
  - invoice-settlement
  - billing-document
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_overview-credit-debit-memos]]"
---

# Debit Memo

**고객이 갚을 금액을 증가시키는 법적 문서.** [[Invoice]]와 **같은 방향** (AR 증가). "추가 청구" 개념.

## 한 줄 정의

> "**Debit memos increase the amount a customer owes.** It is a separate document from the invoice. Debit memos can be used to correct undercharging on an invoice or to levy ad hoc charges outside the context of a subscription." — docs.zuora.com

## ⚠️ 이름 주의

> [!warning] "Debit"이라는 이름에 속지 말 것
> 한국어 직관으로는 "차감 메모" 같지만, **회계 Debit = 자산/비용 증가**. Zuora에서도 마찬가지로 Debit Memo = **잔액 증가 = 추가 청구**. Invoice와 같은 방향.

## AR 영향

| | 방향 | 의미 |
|---|---|---|
| Debit Memo 발행 | **↑ 증가** | 고객이 갚을 금액이 더 늘어남 |

## Credit Memo·Invoice와의 비교

| 오브젝트 | AR 방향 | 발생 상황 |
|---------|--------|---------|
| [[Invoice]] | ↑ | 정상 청구 |
| **Debit Memo** | ↑ | **추가 청구 (과소청구 정정·ad hoc 추가)** |
| [[Credit Memo]] | ↓ | 차감 (과다청구 정정·할인) |
| [[Payment]] | ↓ | 실제 결제 |

## 결제·차감 방법 (공식)

> "Just like an invoice, debit memo balances can be settled by applying either a payment or a credit memo."

즉 Debit Memo의 잔액은:
- **Payment으로 결제**되거나
- **Credit Memo로 차감**됨

Invoice와 동일한 settle 대상으로 취급된다.

## 발행 방식 3가지 (Credit Memo와 공통)

| 방식 | 설명 |
|------|------|
| **Refer to specific invoices** | 특정 Invoice의 undercharge 정정. memo item이 invoice item 참조 |
| **Standalone** | Invoice와 무관한 ad hoc charge. one-time product rate plan charge 참조 |
| Bill run 자동 생성 | (주로 Credit Memo 쪽이지만 원리상 가능) |

## 주요 사용 사례 (공식)

- **Undercharging 정정** — 대표 예시: proration credit 설정이 꺼져 있어 신규 구독 첫 부분월이 청구 안 된 경우 → Debit Memo로 잔액 증가
- **Ad hoc 추가 청구** — 구독과 무관한 일회성 추가
- **Over-delivery 청구** — 고객에게 과배송된 만큼 Debit

## 전제 조건

> [!important] Invoice Settlement 필수
> Credit Memo와 동일. Invoice Settlement 기능 enabled 환경에서만 사용 가능.

## 본인 프로젝트(SAP 연동)와의 관련성

- Debit Memo는 **AR 증가 전표**의 원천 (Invoice와 같은 방향이지만 별도 Document)
- **Invoice와 같은 방향이라도 성격 구분 필요** — 정상 청구 vs 정정/추가 청구. SAP에서 전표 유형을 다르게 잡을 수 있음
- Payment 또는 Credit Memo로 settle 되는 흐름 → SAP 측 반제 처리 연결

## 관련

- [[Invoice]] — 같은 AR 증가 방향, 정상 청구
- [[Credit Memo]] — 반대 방향, 쌍으로 이해
- [[Payment]] — Debit Memo의 결제 수단 1
- Invoice Settlement — 상위 기능 (추후 ingest)
- [[Zuora Billing]]

## 참고 소스

- [[../../../../Sources/docs/zuora/2026-04-18_overview-credit-debit-memos|Sources/docs/zuora/2026-04-18_overview-credit-debit-memos]]
- 공식: [Overview of credit memos and debit memos](https://docs.zuora.com/en/zuora-billing/bill-your-customer/adjust-invoice-amounts/invoice-settlement/credit-memos-and-debit-memos/overview-of-credit-memos-and-debit-memos)
