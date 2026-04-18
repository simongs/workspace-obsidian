---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - invoice
  - billing-document
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_invoice-management]]"
---

# Invoice

**고객에게 보내는 청구서.** Zuora의 4 Documents 중 **가장 기본**이며, 나머지 3개(Payment/Credit Memo/Debit Memo)는 모두 Invoice를 기준으로 움직인다.

## 한 줄 정의

> "An invoice is generated from a **bill run**. Invoices are the bills that you send to your customers." — docs.zuora.com

## AR 영향

| | 방향 | 의미 |
|---|---|---|
| Invoice 발행 | **↑ 증가** | 고객이 갚아야 할 금액이 생김 |

## 생성 시점

Invoice는 **Bill Run**에서 생성된다. Bill Run은 구독/주문을 읽어서 해당 기간의 charge를 모아 Invoice를 찍는 배치.

> [!example] 전형적 흐름
> `Subscription(월 구독)` → `Bill Run` → `Invoice 발행(Posted)` → `Payment 수령` 또는 `Credit Memo 적용`

## 라이프사이클 상태 (공식 문서 언급)

- **Posted Invoice** (발행 완료) — 고객에게 보낸 상태
- Posted Invoice에 가할 수 있는 액션:
  - Change invoice date
  - Delete or reverse an incorrect invoice
  - Write off or reverse posted invoices
  - Split a single invoice into multiple ones

> [!warning] Invoice Settlement 의존
> Invoice reversal, invoice write-off 같은 조작은 **Invoice Settlement 기능이 enabled**되어 있어야 한다. 이 기능이 켜지면 Invoice Adjustment 등의 구 기능을 모두 대체.

## 본인 프로젝트(SAP 연동)와의 관련성

- Invoice는 **AR 증가 전표**의 원천 데이터
- Invoice가 Posted 상태가 되는 시점이 **SAP 연동 트리거**가 되기 쉬움
- Invoice의 line item 단위(한 줄짜리 charge)가 SAP 전표 라인으로 매핑될 가능성
- Invoice 변경(reverse/write-off)은 **SAP 측에 역전표 연동** 필요

## 관련

- [[Payment]] — Invoice를 결제하는 쪽
- [[Credit Memo]] — Invoice 금액을 차감 (과다청구 정정, 할인)
- [[Debit Memo]] — Invoice와 별개의 추가 청구 (과소청구 정정)
- [[Zuora Billing]] — Invoice를 생성하는 제품
- Bill Run — Invoice 생성 배치 (추후 ingest)
- Invoice Settlement — 반제·조정 기능 세트 (추후 ingest)

## 참고 소스

- [[../../../../Sources/docs/zuora/2026-04-18_invoice-management|Sources/docs/zuora/2026-04-18_invoice-management]]
- 공식: [Invoice management](https://docs.zuora.com/en/zuora-billing/bill-your-customer/invoice-management)
