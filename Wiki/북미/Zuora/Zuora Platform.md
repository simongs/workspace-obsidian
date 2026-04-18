---
created: 2026-04-18
updated: 2026-04-18
tags:
  - zuora
  - zuora-platform
  - 북미
sources:
  - "[[Sources/docs/zuora/2026-04-18_zuora-platform-overview]]"
---

# Zuora Platform

**Zuora 제품 suite의 기반 플랫폼.** IT/엔지니어링 팀이 **연결(connect)·확장(scale)·배포(deploy)**하는 공통 레이어. 4 Documents를 SAP으로 넘기는 **연동 경로의 본체**가 여기에 있다.

## 본인 프로젝트에서 가장 중요한 4영역

> [!important] SAP 연동 관점 우선순위
> 1. **Integration** — API·Integration Hub·Data Warehouse Connector (연동 수단)
> 2. **Extensibility** — Events and Notifications (webhook)·Custom Fields (추적키)·Workflow
> 3. **Data** — Data Query (SQL 조회)·Reporting
> 4. **Data Management** — Data Loader·Environments (마이그레이션)

## 7개 카테고리 전체 구조

| 영역 | 하위 기능 | 작업 관련성 |
|------|---------|-----------|
| **System Management** | Command Center · Settings Service · System Health | △ 운영 |
| **Data Management** | Data Loader · Deployment Manager · DevOps · Environments | 🟡 마이그레이션·환경 |
| **Org & Entity Management** | Multi-Org · Multi-Entity | ⭐ HMA/KUS 다중 법인 분리 |
| **Extensibility** | Attachments · Custom Fields · Custom Logic · Custom Objects · **Events and Notifications** · Mediation · Object Manager · Workflow | ⭐⭐ Webhook push, 추적키 |
| **Integration** | **APIs** · **Integration Hub** · SDKs · **Connector for Data Warehouses** · Secure Data Share for Snowflake | ⭐⭐⭐ SAP 연동 본체 |
| **Data** | **Data Query** · Data Warehouse · Reporting · Analytics · Object Manager | ⭐ 배치 pull |
| **Security and Identity** | Administrator Settings · Audit Trail · Zuora OneID · Data Protection · Event Streaming Connector · Zuora Protect | 🟡 감사·PII |

## SAP 연동 기술 경로 3가지 (공식 옵션)

| # | 방식 | 사용 컴포넌트 | 성격 |
|---|------|----------|------|
| ① | **실시간 push** | Events and Notifications (callout/webhook) | Invoice posted·Payment created 이벤트 → HMG 미들웨어 |
| ② | **배치 pull** | Data Query·REST API | 정기 조회로 수집 |
| ③ | **벌크 export** | Connector for Data Warehouses·Secure Data Share for Snowflake | 대용량·분석용 |

> [!warning] 실제 선택은 회사 PC vault로
> HMG 북미가 ①②③ 중 무엇을 쓰는지(또는 혼합)는 회사 내부 결정. 개인 vault에서는 "선택지가 무엇인지"만 정리.

## 주목할 하위 개념 (추후 ingest 대상)

| 개념 | 용도 |
|------|------|
| **Events and Notifications (Callouts)** | 이벤트 기반 실시간 Webhook |
| **Data Query** | Zuora의 SQL-like 조회 언어 |
| **Custom Fields** | 표준 오브젝트에 추적키/연계 ID 저장 |
| **Workflow** | 조건 기반 자동화 |
| **Multi-Entity** | 1 테넌트 다중 법인 |
| **Integration Hub** | 사전 제작 커넥터 집합 |

## 관련

- [[Zuora Billing]] · [[Zuora Payments]] · [[Accounts Receivable]] — 이 플랫폼 위에서 동작하는 제품들
- [[Invoice]] · [[Payment]] · [[Credit Memo]] · [[Debit Memo]] — 연동 대상 오브젝트
- [[Paymentech]] — 실제 PG (세션 C — 본인 판단상 후순위)

## 참고 소스

- [[../../../Sources/docs/zuora/2026-04-18_zuora-platform-overview|Sources/docs/zuora/2026-04-18_zuora-platform-overview]]
- 공식: [Zuora Platform overview](https://docs.zuora.com/en/zuora-platform/overview)
