---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - zuora-billing
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_zuora-billing-overview]]"
---

# Zuora Billing

Zuora의 핵심 제품. **구독 기반 비즈니스를 위한 recurring billing 소프트웨어**로 가격 책정, 구독 관리, 인보이스 발행, 글로벌 결제 오케스트레이션을 하나의 플랫폼에서 제공한다.

## 4 Documents 관점의 역할

> [!important] 본인 프로젝트(4 Documents → SAP 연동)와의 접점
> HMG 4 Documents (Invoice · Credit Memo · Payment · Refund) 중 **Invoice · Credit Memo**가 Zuora Billing에서 생성된다. 남은 **Payment · Refund**는 [[Zuora Payments]]에서 생성되지만, Billing과 Payments는 "packaged together"로 통합 동작한다.
>
> Zuora 공식 Billing Document에는 **Debit Memo**도 포함되지만, HMG 프로젝트의 4 Documents에는 들어가지 않는다.

## 핵심 기능 (3그룹 재편)

공식 문서의 8개 capabilities를 역할별로 재배치.

| 그룹 | 기능 | 작업 관련성 |
|------|------|-----------|
| **가격/구독 설계** | Flexible pricing and packaging · Subscription order management and hybrid billing · Integrated quoting | 🟡 업스트림 (데이터 생성 이전) |
| **청구 실행** | Usage-based metering and billing · E-invoicing and tax compliance · Global payments orchestration | ⭐ Invoice/Payment 생성 지점 |
| **플랫폼 특성** | API-first and built to scale · Built-in extensibility | ⭐ SAP 연동 관점 |

### 눈여겨볼 포인트

- **40+ pre-built payment gateway connectors, 20+ payment methods** — [[Paymentech]]도 이 중 하나. Zuora가 PG 오케스트레이터 역할.
- **mid-cycle 가격/청구 조정이 downstream 재무 프로세스에 영향 없음** — 구독 변경(업그레이드/다운그레이드) 유연성. 반제 전략과 직결.
- **Hybrid billing** — one-time + recurring + usage 모두 한 엔진. "[[일회성 결제 vs 구독 결제]]" 흐름 차이의 근원.

## Business Tasks — SAP 연동 접점

공식 문서가 나열하는 6개 작업 중 핵심 접점 하나:

> [!tip] 공식 SAP 연동 접점
> **"Auto-upload transactions to Zuora Finance, Zuora Revenue, or your own accounting software"**
>
> Zuora는 자사 SaaS(Finance/Revenue)로 밀어넣기를 권장하지만, **"your own accounting software"로 export**도 공식 지원. 이 경로가 본인 프로젝트의 접점. 구체적 방식(API pull / Event callout / Data Query / scheduled export)은 다음 세션에서 파악.

나머지 작업:
- Build product catalog with charge models
- Manage customer lifecycle (accounts / subscriptions / orders / order line items)
- Bill customers and let them pay billing documents
- Generate/export reports on subscription, usage, billing, payment metrics
- Monitor & audit with extensibility

## 하위 개념 시드 (앞으로 파낼 것)

- **Product Catalog · Charge Model** — 가격 체계
- **Account · Subscription · Order · Order Line Item** — 고객 계약 단위
- **Billing Document** — Invoice · Credit Memo · Debit Memo의 **상위 개념** (Zuora 공식 용어)
- **Transaction** — Billing Document + Payment + Refund 통칭 가능성

## 관련

- [[Zuora]] — 상위 플랫폼
- [[Zuora Payments]] — Payment 생성 및 PG 연동
- [[Zuora Platform]] — API, 통합, Extensibility
- [[Invoice]] · [[Credit Memo]] · [[Debit Memo]] · [[Payment]] — 4 Documents (세션 A-2에서 정식 ingest)
- [[Paymentech]] — 실제 연동 PG (세션 C)

## 참고 소스

- [[../../../Sources/docs/zuora/2026-04-18_zuora-billing-overview|Sources/docs/zuora/2026-04-18_zuora-billing-overview]]
- 공식: [Zuora Billing overview](https://docs.zuora.com/en/zuora-billing/overview)
