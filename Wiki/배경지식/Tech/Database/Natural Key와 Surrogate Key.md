---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Database
related:
  - "[[카디널리티와 선택성]]"
  - "[[Fractional Indexing]]"
sources:
  - "[[Sources/ai-queries/2026-08-20_데이터-모델링과-순서-관리]]"
created: 2026-08-20
updated: 2026-08-20
tags:
  - 0🌲
  - wiki
  - database
---

## 정의

**Natural Key(자연키)** 는 업무 도메인에 이미 존재하며 그 자체로 행을 유일하게 식별하는 값이다.
**Surrogate Key(대리키)** 는 식별만을 위해 시스템이 생성한, 업무적 의미가 없는 값이다.

## 비교

| 구분 | Natural Key | Surrogate Key |
|------|-------------|---------------|
| 출처 | 업무 도메인 | 시스템 생성 |
| 예시 | 사업자등록번호, ISBN, 이메일, `invoice_no` | `AUTO_INCREMENT`, UUID |
| 의미 | 있음 (사람이 읽고 이해) | 없음 |
| 변경 가능성 | **있음** | 없음 |
| 주 용도 | 업무 조회, 외부 시스템 대조 | FK 참조, 내부 식별 |

## 자연키를 PK로 쓰지 않는 이유

자연키는 **바뀐다**. 그리고 PK가 바뀌면 FK로 참조하는 모든 테이블이 연쇄로 깨진다.

> [!failure] 자연키의 배신 사례
> - "주민번호는 절대 안 바뀐다" → 체계 개정
> - "이메일은 유일하다" → 사용자가 이메일을 변경
> - "외부 시스템 ID는 고정이다" → 시스템 마이그레이션으로 재발급

## 실무 정석

**PK는 surrogate, natural key는 UNIQUE 제약으로 별도 보장.**

```sql
CREATE TABLE invoice (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,  -- surrogate: FK 참조용
    invoice_no  VARCHAR(30) NOT NULL UNIQUE,        -- natural key: 업무 조회용
    ...
);
```

두 역할을 분리하면 자연키가 바뀌어도 참조 무결성이 깨지지 않는다.

## Hibernate `@NaturalId`

```kotlin
@Entity
class Invoice(
    @Id @GeneratedValue
    val id: Long? = null,

    @NaturalId(mutable = false)   // 불변 자연키 선언
    val invoiceNo: String,
)

val invoice = session.byNaturalId(Invoice::class.java)
    .using("invoiceNo", "INV-2026-0001")
    .load()
```

실익 세 가지:

| 이점 | 내용 |
|------|------|
| **2차 캐시 활용** | `byNaturalId()`는 자연키→PK 매핑을 캐시한다. 같은 조건의 JPQL은 매번 DB를 치지만 이건 캐시 히트 시 쿼리가 안 나감 |
| **불변성 강제** | `mutable = false`면 값 변경 시 예외. "바뀌면 안 되는 업무 키"라는 도메인 규칙을 코드 레벨에서 보장 |
| **의도 문서화** | 어노테이션 자체가 "이 컬럼이 엔티티의 업무적 정체성"이라는 선언 |

## 정산 도메인 적용

| 엔티티 | 자연키 후보 |
|--------|-----------|
| SAP 전표 | `전표번호 + 회계연도 + 회사코드` (복합 자연키) |
| Invoice | `invoice_no` |
| Zuora 연동 개체 | Zuora 발급 `zuora_id` |

> [!important] 외부 시스템 ID는 전형적인 natural key
> [[Zuora Platform]]·SAP 등 외부 시스템이 발급한 ID를 **PK로 쓰지 말 것**. 내부 PK를 따로 두고 외부 ID는 UNIQUE + `@NaturalId`로 관리해야, 외부 시스템 마이그레이션이나 ID 재발급이 발생해도 내부 참조 무결성이 유지된다.

## 관련 개념

- [[카디널리티와 선택성]] — 1:1 관계에서 FK 겸 PK로 쓰는 `member_id`도 자연키 성격을 띤다
- [[Kotlin]] — JPA 엔티티 정의

## 참고 소스

- [[Sources/ai-queries/2026-08-20_데이터-모델링과-순서-관리]]
