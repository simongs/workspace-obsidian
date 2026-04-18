---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - zuora-payments
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_zuora-payments-overview]]"
---

# Zuora Payments

**"인보이스를 현금으로 바꾸는" end-to-end 결제 솔루션.** 결제 수단·PG 연동·자동 콜렉션(dunning)까지 커버. [[Zuora Billing]]과 packaged together로 동작한다.

## 4 Documents 관점의 역할

> [!important] 본인 프로젝트와의 접점
> HMG 4 Documents 중 **Payment · Refund** 2개의 생성 주체. Billing이 만든 Invoice를 받아 실제 돈을 회수하고([[Payment]]), 필요 시 돈을 되돌려 보낸다([[Refund]]).

## 핵심 키워드 3개

| 키워드 | 의미 | 작업 관련성 |
|--------|------|-----------|
| **Payment gateways** | 40+ 사전 연동 PG 중 선택 — [[Paymentech]] 포함 | ⭐ 세션 C에서 정식 ingest |
| **Automated collections / dunning** | 미결제 고객 자동 독촉·재시도 + ML 최적화 | 🟡 반제·write-off 전략과 연관 |
| **Turn invoices into cash** | Invoice → Payment 전환 주체 | ⭐ Payment 레코드 생성 메커니즘 |

## 하위 영역 (Overview 카드 기준)

공식 Browse by product 카드에서 명시한 sub-area:
- **Overview**
- **Manage payment gateway integrations and payment methods** — PG 연동 구조
- **Process payments** — Payment 처리 방식 (Payment Schedule, Payment Run 등)

## 생길 하위 개념 (추정)

- **Payment Method** — 카드/ACH/PayPal 등 결제 수단 타입
- **Payment Gateway** — PG 커넥터 (Paymentech, Stripe, Worldpay 등)
- **Payment Run** — 스케줄링된 결제 배치
- **Payment Schedule** — 분할 결제 일정
- **Dunning** — 실패 결제 재시도 전략
- **Refund** — Payment 역방향 트랜잭션 (Credit Memo와 별도)

## 주의사항

> [!warning] Overview 페이지가 얇다
> 공식 Overview는 한 문단 수준이라 Payment 오브젝트의 필드/생명주기를 파악하기 어렵다. 세부는 **세션 C (Paymentech)** 또는 **세션 A-2 (4 Documents)**에서 하위 페이지 `Process payments`를 봐야 보강됨.

## 관련

- [[Zuora]] — 상위 플랫폼
- [[Zuora Billing]] — 함께 packaged, Invoice 생성
- [[Zuora Platform]] — API/Integration 기반
- [[Payment]] — HMG 4 Documents 중 하나
- [[Refund]] — HMG 4 Documents 중 하나 (Payment 역방향)
- [[Paymentech]] — 실제 연동 PG

## 참고 소스

- [[../../../Sources/docs/zuora/2026-04-18_zuora-payments-overview|Sources/docs/zuora/2026-04-18_zuora-payments-overview]]
- 공식: [Zuora Payments overview](https://docs.zuora.com/en/zuora-payments/overview)
