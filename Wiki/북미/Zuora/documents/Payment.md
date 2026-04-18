---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - payment
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_process-payments]]"
---

# Payment

**고객이 실제로 낸 돈.** 4 Documents 중 유일하게 **[[Zuora Payments]]**가 생성 주체이며 (나머지 3개는 [[Zuora Billing]]), 결제 게이트웨이([[Paymentech]])와의 상호작용 결과물이다.

## 한 줄 정의

> "Turn invoices into cash." — Payment은 Invoice를 결제해서 **AR을 감소시키는** 트랜잭션.

## AR 영향

| | 방향 | 의미 |
|---|---|---|
| Payment 수령 | **↓ 감소** | 고객이 갚았으므로 채권이 줄어듦 |

## 생성 경로 (7가지 공식 방법)

Payment은 다양한 integration approach로 생성된다:

| 방법 | 설명 |
|------|------|
| Payment Forms | 호스티드 결제 폼 (embed) |
| Payment Pages 2.0 | Zuora가 호스팅하는 결제 페이지 |
| Payment Links | URL 기반 결제 |
| JavaScript SDK | 프론트엔드 직접 통합 |
| **Payment Runs** | **스케줄 배치 결제** — 구독 갱신에 주로 쓰임 |
| **API operations** | **REST API로 직접 결제 생성** |
| Zuora UI | 운영자가 수동 입력 |

## 결제 아키텍처 기능

공식 문서에서 강조하는 Payment 처리 능력:

- 여러 통합 방식 지원 (hosted pages, forms, SDK, API, scheduled runs)
- 지역·비즈니스별 결제 수단/게이트웨이 구성 가능
- **Retry logic, surcharge handling, custom rules** 구성
- **Zuora ↔ merchant 계정 간 reconciliation** (대사)
- 엣지 케이스 처리: **unapplied payments, delayed capture, transaction errors**

## 본인 프로젝트(SAP 연동)와의 관련성

- Payment은 **AR 감소 전표**의 원천
- **Payment Run**이 월 구독 갱신의 핵심 배치 — 대량 Payment가 한 번에 생성됨
- **Unapplied Payment** 개념 주의 — 고객이 돈을 냈지만 어느 Invoice에 적용될지 미정인 상태. SAP 연동 시 적용 시점/상태 관리 중요
- **Paymentech와의 reconciliation**은 별도 레이어 (PG 대사와 회계 대사)

## 관련 개념 (추후 ingest)

- **Payment Method** — 카드/ACH/PayPal 등 결제 수단 타입
- **Payment Gateway** — PG 커넥터 ([[Paymentech]] 등)
- **Payment Run** — 스케줄 결제 배치
- **Payment Schedule** — 분할 결제 일정
- **Dunning** — 실패 재시도 전략
- **Refund** — Payment의 역방향 트랜잭션 (Credit Memo의 후속 액션으로도 가능)
- **Unapplied Payment** — Invoice에 매칭되지 않은 Payment

## 관련

- [[Invoice]] — Payment의 적용 대상
- [[Credit Memo]] — Payment 없이 AR을 감소시키는 대안 (환불성 장부 처리)
- [[Refund]] — Payment의 역방향 트랜잭션 (실제 현금 유출)
- [[Zuora Payments]] — Payment 생성 제품
- [[Paymentech]] — 실제 연동 PG

## 참고 소스

- [[../../../../Sources/docs/zuora/2026-04-18_process-payments|Sources/docs/zuora/2026-04-18_process-payments]]
- 공식: [Process payments](https://docs.zuora.com/en/zuora-payments/process-payments/process-payments)
