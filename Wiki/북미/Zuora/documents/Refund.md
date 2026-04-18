---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - refund
  - zuora-payments
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_refunds]]"
---

# Refund

**[[Payment]]의 역방향 트랜잭션 — 실제 돈을 고객에게 되돌려주는 오브젝트.** HMG 북미 프로젝트의 **4 Documents** 중 하나 (Invoice / Credit Memo / Payment / Refund).

## 한 줄 정의

> "Zuora allows you to **issue and track refunds on payments**." — docs.zuora.com

[[Credit Memo]]가 **장부 상 AR 차감**이라면, Refund는 **실제 현금 반환**. 두 개는 같은 이벤트의 다른 레이어.

## AR 및 현금 영향

| | AR 영향 | 현금 |
|---|---|---|
| [[Credit Memo]] 발행만 | ↓ 감소 | 변화 없음 |
| **Refund 실행** | (Credit Memo에서 이미 감소) | **↓ 실제 지출** |
| Non-referenced refund | 변화 없음 (credit balance 소진) | ↓ 실제 지출 |

## 두 가지 유형 ⭐

Zuora Refund는 두 가지 유형으로 나뉜다:

### ① Referenced Refund (참조 환불)

- **원본 Payment와 linked**
- **동일한 Payment Method + 동일한 Payment Gateway**로만 처리 가능
- 원본 gateway가 deactivated되면 처리 불가 (에러)

> [!warning] Gateway 전환 시 주의
> 기존 PG(예: 구버전 Paymentech)에서 신버전으로 전환 후, 구 gateway로 처리된 payment를 환불할 때:
> - 옵션 1: 구 gateway를 active로 유지해 referenced refund 처리
> - 옵션 2: 신 gateway에서 **non-referenced refund** 생성
> - Gateway 계정 자체가 마이그레이션됐으면 옵션 2만 가능

### ② Non-Referenced Refund (비참조 환불) = Credit Balance Refund

- **특정 Payment에 linked 안 됨**
- Payment Method/Gateway 제약 없음
- 신용카드 정보가 없는 경우 **수동 체크 발행** 대안 가능
- **Credit Balance** 기능 필요 (legacy)

> [!important] Invoice Settlement 모드에서는 달라짐
> **Invoice Settlement 기능 enabled** 환경에서는 **Credit Balance가 deprecated**. 그래서 "non-referenced refund = Credit Memo에 대한 Refund"로 바뀜. → **Refund Credit Memos** 경로 사용.
>
> HMG 북미가 Invoice Settlement 켰는지 여부 확인 필요.

## ③ External Refund

Zuora **밖에서 실행한 refund**를 **Zuora에 기록만** 하는 것. 예: 수표 발행, gateway virtual terminal에서 직접 환불.

Zuora Payments의 External Payment 개념과 대칭.

## Refund Credit Memo 플로우 (Invoice Settlement 환경)

> [!example] Credit Memo → Refund 흐름
> ```
> [청구 오류 발견 / 구독 중도 해지]
>       │
>       ▼
> [Credit Memo 발행(Posted)]   ── AR 장부 감소
>       │                          (현금은 아직 움직이지 않음)
>       ▼
> [Application Rule 설정]       ── refund 방식 규칙
>       │
>       ▼
> [Refund 실행 (full or partial)]
>       │
>       ├─ Electronic → Gateway 호출 → 고객 결제수단으로 환불
>       └─ External → 수표 등 off-line 처리 후 기록
>       │
>       ▼
> [Credit Memo part 소진]
>       │
>       ▼
> [PG Reconciliation + SAP 전표]
> ```

## 시간 제약 (Gateway 의존)

Zuora 자체는 refund 시점 제한 없음. 하지만 **electronic refund는 gateway 정책 종속**:

- 일반: **90일 이내**
- 게이트웨이마다 다름 (30일 제한 있는 곳도)
- API refund vs virtual terminal refund가 제한 다를 수 있음
- 기간 초과 시 gateway 에러 (예: PayPal `10009 - You are over the time limit`)

> [!warning] 운영 시나리오
> 연간 구독을 1년 지난 뒤 취소+환불하려 할 때, Paymentech가 90일 제한이라면 referenced refund **실패**. 이 경우:
> - Non-referenced refund (credit card 정보 있으면)
> - External refund (수표 등 off-line)
> - Credit Memo만 발행 후 Refund 생략 (다음 invoice에 apply)

## 운영 Operation

| Zuora UI | API |
|---------|-----|
| Create refund · Delete refund · Manage chargebacks · Prevent chargebacks | Refund a payment · POST Refund Credit Memo |

## 알려진 이슈

**"Refunds stuck in processing" (Trial balance 이후)**: 같은 account에 **동시에 여러 refund 생성** 시 gateway는 approved 보내도 Zuora 레코드는 "processing"으로 남음. 해결:
- 통합 로직에서 동시 refund 방지
- UI에서 레코드 열어 status를 "processed"로 수동 업데이트

## HMG 프로젝트 "4 Documents" 맥락 ⭐

> [!important] 왜 Refund가 Debit Memo 대신 4 Documents에 포함되는가
> HMG 북미의 4 Documents 구성 = **Invoice · Credit Memo · Payment · Refund**.
>
> 추정 근거:
> - 구독 SaaS에서 **Debit Memo 유스케이스(undercharge 정정)는 드묾**
> - **Refund는 구독 취소·환불 시나리오가 많아 매우 중요**
> - 현금 이동이 실제 발생하는 이벤트는 SAP 전표 생성에 결정적 (AR 차감만 있는 Credit Memo만으로는 현금 회계 부족)
>
> → Debit Memo는 **존재하지만 별도 플로우**로 다루거나, 없거나, 드물게 사용. 실제 구성은 회사 내부 확인 필요.

## 본인 프로젝트(SAP 연동)와의 관련성

- Refund는 **현금 지출 전표**의 원천 — Credit Memo(AR 차감)와는 **별도 전표**로 SAP에 반영 필요
- **Gateway reconciliation**과 밀접: PG에서 실제 돈이 나간 것을 확인
- **Chargeback** (고객이 카드사 통해 강제 환불)도 이 범주 — 예외 처리 로직 필요
- **External refund**는 수작업 입력 기반이므로 SAP 전표와의 무결성 체크 중요

## 관련

- [[Payment]] — Refund의 대상 (referenced) 또는 역방향
- [[Credit Memo]] — Invoice Settlement 환경에서 Refund의 전 단계
- [[Zuora Payments]] — Refund 처리 제품
- [[Paymentech]] — Gateway 의존성 (세션 C)
- **Credit Balance** — legacy, Invoice Settlement 환경에선 deprecated
- **Chargeback** — 강제 refund (추후 ingest)
- **External Refund** — 오프라인 환불 기록 (추후 ingest)

## 참고 소스

- [[../../../../Sources/docs/zuora/2026-04-18_refunds|Sources/docs/zuora/2026-04-18_refunds]]
- 공식 Refund overview: [Refunds](https://docs.zuora.com/en/zuora-payments/process-payments/process-payments/manage-payment-processing/manage-payment-processing/refunds)
- 공식 Refund Credit Memos: [Refund credit memos](https://docs.zuora.com/en/zuora-billing/bill-your-customer/adjust-invoice-amounts/invoice-settlement/credit-memos-and-debit-memos/management-of-debit-and-credit-memos/refund-credit-memos)
