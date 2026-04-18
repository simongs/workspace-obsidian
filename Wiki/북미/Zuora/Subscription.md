---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - subscription
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_get-started-with-subscriptions]]"
---

# Subscription (Zuora)

**구독**은 Zuora의 핵심 계약 단위. Invoice가 여기서 bill run을 통해 생성되며, 4 Documents 모두 Subscription에 뿌리를 둔다.

## 근본 구분: Termed vs Evergreen ⭐

Zuora Subscription의 **가장 본질적 축**. 사용자가 말한 "연간/월간 구독"은 **Termed 구독의 Initial Term 차이**.

| | **Termed Subscription** | **Evergreen Subscription** |
|---|---|---|
| 종료일 | 있음 (Term End Date) | 없음 |
| 기본 설정 | **Default** | 명시적 선택 필요 |
| 필수 필드 | Initial Term, Renewal Setting, Term Start Date, Term End Date, Renewal Term | 없음 (위 필드 모두 미적용) |
| 갱신 | Auto Renew 설정 가능 | 갱신 개념 없음 (해지까지 계속) |
| TCV (Total Contract Value) | 계산 가능 | **빈 값** (end date 없으므로 계산 불가) |
| 예시 | **연간 SaaS**, **월간 멤버십** | Netflix, Comcast, SurveyMonkey |

### 사용자 관점 번역

| 사용자 용어 | Zuora 용어 |
|-----------|---------|
| **연간 구독** | Termed, Initial Term = 12 months |
| **월간 구독** | Termed, Initial Term = 1 month (+ Auto Renew) |
| **"해지까지 자동"** | Evergreen |

## Renewal Setting (Termed 전용)

Termed 구독이 Term End에 도달할 때의 동작:

| 옵션 | 효과 |
|------|------|
| `Renew With Specific Term` | Renewal Term 만큼 재갱신, 계속 Termed |
| `Renew To Evergreen` | 첫 Term 끝나면 Evergreen으로 전환 |

## Auto-Renew 동작

**Auto-Renew ON**:
- Term End Date 도달 시 **Renewal Amendment 자동 생성**
- 설정에 따라 Termed 연장 또는 Evergreen 전환

**Auto-Renew OFF**:
- Term End Date 도달 시 **Out of Term** 상태로 진입
- ⚠️ **Zuora가 자동 취소하지 않음**. Invoice도 발행 안 됨
- 수동 개입(Renew 또는 Cancel) 필요

> [!warning] Out of Term 상태 주의
> "Auto-Renew OFF이면 자동 해지될 것"이라는 직관과 반대. 명시적 Cancel이 없으면 상태만 멈춰 있다. 운영에서 chase 없으면 **유령 구독**이 쌓일 수 있음.

## Subscription Version (구독 이력)

Amendment가 발생할 때마다 **새로운 Subscription Version**이 생성됨.

- Renewal, product 업데이트/추가/제거 → 새 version
- **새 rate plan charge 레코드**가 `OriginalId`로 원본 참조하며 함께 생성됨
- 즉, 하나의 Subscription 번호 아래 여러 version이 존재하는 구조

> [!important] SAP 연동 관점
> 한 Subscription의 여러 version이 서로 다른 Invoice를 낼 수 있다. **"어느 version의 Invoice인지" 추적이 필요**할 수 있음. `OriginalId` 체인을 SAP 측 전표에 연결할지 결정 필요.

## 주요 날짜 필드

| 필드 | 수준 | 의미 |
|------|-----|------|
| **Subscription date** | Subscription | charges가 발효되는 시작일 |
| **Bill cycle date** | Account | 매달 몇일에 청구하는지 (예: 5일) |
| **Term Start Date** | Subscription | Termed 구독의 term 시작 |
| **Term End Date** | Subscription | Termed 구독의 term 종료 |
| **Next Charge Date** | **RatePlanCharge** | 다음 charge 발생 예정일 |
| **Next Renewal Date** | **RatePlanCharge** | 다음 renewal 예정일 |
| **ChargedThroughDate** | **RatePlanCharge** | 이미 invoice된 마지막 기간 + 1 (예: 5/1~5/31 invoice → 6/1) |

> 주목: **Next Charge / Next Renewal / ChargedThroughDate는 RatePlanCharge 레벨**. 한 Subscription이 여러 charge를 다른 시점에 시작시킬 수 있으므로.

## Owner 분리

- **Subscription Owner** ≠ **Invoice Owner** 가능
- Default: 같은 account
- 서로 다른 account로 설정 가능 (parent-child 관계 필수 아님, 관례로 많이 씀)
- HMG 그룹사 시나리오에서 의미 있을 수 있음 — 구독자는 end user지만 인보이스는 기업 법인으로

## 일회성 결제 vs 구독 결제 — 사용자 관심 주제

공식 Zuora 구분:

| | **Subscription Transaction** | **Non-Subscription Transaction** |
|---|---|---|
| 성격 | Recurring (monthly flat / quarterly per-unit / usage) + one-time activation | One-time 일회성 |
| 표현 단위 | Subscription + Rate Plan + Rate Plan Charge | **Order Line Item** |
| 사례 | 월/연 구독, 사용량 기반 | 물리적 상품 판매, 전문 서비스, 일회성 수수료 |
| 연결 | Subscription이 루트 | Independent 또는 기존 Subscription과 연계 가능 |

> [!tip] 사용자가 궁금해한 "일회성 vs 월 구독 결제의 인보이스/페이먼트 흐름"
> - **월 구독**: Subscription → Bill Run → Invoice → Payment Run으로 자동 결제
> - **일회성**: Order Line Item → Invoice (독립) → 수동 또는 API Payment
> - 인보이스는 **동일하게 [[Invoice]] Document**로 생성됨 (타입만 다름)
>
> 이 흐름은 세션 B(구독 라이프사이클 · 일회성 vs recurring 플로우)에서 더 깊이.

## 전제 — Subscription 생성 조건

1. **Product Catalog** — 제품 구성 (Rate Plan, Rate Plan Charge)
2. **Customer Account** — 고객 계정
3. 그 위에서 Subscription 생성

## 관련

- [[Invoice]] — 구독의 bill run이 생성
- [[Payment]] — 구독 청구를 결제
- [[Credit Memo]] · [[Debit Memo]] — 구독 변경·정정 시 발행
- [[Zuora Billing]] — 구독 관리 제품
- **Amendment** — 구독 변경 단위 (추후 ingest)
- **Rate Plan / Rate Plan Charge** — 구독의 charge 단위 (추후 ingest)
- **Order / Order Line Item** — 비구독 트랜잭션 (추후 ingest)
- **Bill Run** — Invoice 생성 배치 (추후 ingest)
- **Product Catalog** — 구독 전제 조건

## 참고 소스

- [[../../../Sources/docs/zuora/2026-04-18_get-started-with-subscriptions|Sources/docs/zuora/2026-04-18_get-started-with-subscriptions]]
- 공식: [Get started with subscriptions](https://docs.zuora.com/en/zuora-billing/manage-accounts-subscriptions-and-non-subscriptions/manage-subscription-transactions/common-subscription-information/get-started-with-subscriptions)
