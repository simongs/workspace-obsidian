---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - credit-memo
  - invoice-settlement
  - billing-document
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_overview-credit-debit-memos]]"
---

# Credit Memo

**고객의 잔액(AR)을 감소시키는 법적 문서.** 과다청구 정정, 할인, 분쟁 해결, 반품 등에 사용. [[Invoice]] 반대 방향.

## 한 줄 정의

> "**Credit memos reduce invoice and account balances.** By applying one or more credit memos to invoices with positive balances, you can reduce the invoice balances in the same way that you apply a payment to an invoice." — docs.zuora.com

즉 **"결제 대신 쓰는 잔액 차감"** — Payment처럼 AR을 줄이지만 실제 돈이 오가진 않음.

## AR 영향

| | 방향 | 의미 |
|---|---|---|
| Credit Memo 발행 | **↓ 감소** | 고객이 갚을 금액 차감 (과다청구/할인/환급성) |

## Debit Memo·Invoice와의 비교

| 오브젝트 | AR 방향 | 발생 상황 |
|---------|--------|---------|
| [[Invoice]] | ↑ | 정상 청구 |
| [[Debit Memo]] | ↑ | 추가 청구 (과소청구 정정) |
| **Credit Memo** | ↓ | **차감 (과다청구 정정·할인·환급성)** |
| [[Payment]] | ↓ | 실제 결제 |

## ⚠️ Refund와 혼동 주의

> [!warning] Credit Memo ≠ Refund
> - **Credit Memo**: AR(장부) 차감. 실제 현금은 움직이지 않음.
> - **Refund**: Credit Memo의 **잔액을 고객에게 현금으로 돌려주는** 후속 액션. 별도 트랜잭션.
>
> 공식 기능 리스트에 "Refund a certain amount of the credit memo balance to your customer"라고 명시되어 있음 — Refund는 Credit Memo의 파생.

## 발행 방식 3가지 (공식)

| 방식 | 설명 |
|------|------|
| **Refer to specific invoices** | 특정 Invoice의 item 조정. memo item이 invoice item 참조 |
| **Standalone** | Invoice와 무관한 ad hoc credit. one-time product rate plan charge 참조. 양수 잔액이면 임의 invoice/debit memo에 적용 가능 |
| **Generate by bill run** | bill run이 **negative charge 이벤트**를 자동 Credit Memo로 변환. AR 반제 관리에 유리 |

## 주요 사용 사례 (공식)

- 가격 오류 정정 (과다청구)
- Proration credit (선불 서비스 취소 → 미사용분 환급)
- Charge dispute 해결
- 기간 한정 할인
- Ad hoc credit (loyalty credit 등)
- Write off account balances (bad debt를 0으로 만들기)
- Negative charge 반영

## 전제 조건

> [!important] Invoice Settlement 필수
> Credit Memo는 **Invoice Settlement 기능이 enabled**된 테넌트에서만 사용 가능. 이 기능은 구 Invoice Adjustments / Invoice Item Adjustments / Credit Balance Adjustments를 **전면 대체**한다.

## 법적 요건

유럽을 비롯한 많은 국가에서 **Credit/Debit Memo는 invoice 조정의 법적 요구사항**. AR 감사 및 채권 회계 정확성 확보에 유리.

## 본인 프로젝트(SAP 연동)와의 관련성

- Credit Memo는 **AR 감소 전표**의 원천 (Payment과는 별도 성격)
- **Apply/Unapply 상태 추적** 필요 — 어느 Invoice에 얼마나 적용됐는지
- **Bill run 자동 생성** 케이스가 많음 → 대량 이벤트 처리 고려
- **Refund 연결**: Credit Memo → Refund로 이어지는 경우 실제 현금 환급까지 SAP 연동 필요
- **Closed accounting period** 대응: 마감된 기간에도 apply/unapply 가능 — 과거 기간 수정 시 SAP 재연동 이슈

## 기능 (공식)

- Invoices / one-time charges 기반 생성
- Invoice나 Debit Memo에 apply/unapply/reapply
- **Refund** (Credit Memo 잔액을 현금 환급)
- 세금 계산 (Zuora Tax, Avalara, 3rd-party)
- 메모 템플릿 커스터마이징 + PDF 생성
- Reverse invoice / Write off invoice (write-off 시 자동 Credit Memo 생성)
- Create/Post 시 Callout·Email 알림
- REST API preview
- Payment Run 중 auto-apply 설정
- Closed period에도 apply/unapply 가능

## 관련

- [[Invoice]] — 적용 대상
- [[Debit Memo]] — 쌍으로 이해해야 하는 반대 방향
- [[Payment]] — 유사한 AR 감소 효과지만 실제 현금
- Refund — Credit Memo 후속 액션 (추후 ingest)
- Invoice Settlement — 상위 기능 (추후 ingest)
- [[Zuora Billing]]

## 참고 소스

- [[../../../../Sources/docs/zuora/2026-04-18_overview-credit-debit-memos|Sources/docs/zuora/2026-04-18_overview-credit-debit-memos]]
- 공식: [Overview of credit memos and debit memos](https://docs.zuora.com/en/zuora-billing/bill-your-customer/adjust-invoice-amounts/invoice-settlement/credit-memos-and-debit-memos/overview-of-credit-memos-and-debit-memos)
