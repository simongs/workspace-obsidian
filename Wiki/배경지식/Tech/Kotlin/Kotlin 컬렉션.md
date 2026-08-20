---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Kotlin
related:
  - "[[Kotlin]]"
  - "[[Kotlin 제어 흐름]]"
  - "[[Stream API]]"
sources:
  - "[[Kotlin Basic Syntax - kotlinlang.org]]"
  - "[[Sources/ai-queries/2026-08-20_Stream-API와-flatMap]]"
created: 2026-04-12
updated: 2026-08-20
tags:
  - 0🌲
  - wiki
---

## 정의

Kotlin 표준 라이브러리는 풍부한 컬렉션 연산을 제공한다. 함수형 스타일로 데이터를 변환하고 필터링할 수 있다.

## 기본 컬렉션 타입

| 타입 | 특징 | 생성 함수 |
|------|------|-----------|
| `List` | 순서 있음, 중복 허용 | `listOf()` |
| `Set` | 순서 없음, 중복 불가 | `setOf()` |
| `Map` | 키-값 쌍 | `mapOf()` |

## 함수형 컬렉션 연산

```kotlin
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
fruits
    .filter { it.startsWith("a") }     // 조건 필터링
    .sortedBy { it }                    // 정렬
    .map { it.uppercase() }             // 변환
    .forEach { println(it) }            // 순회
// 출력: APPLE, AVOCADO
```

### 주요 연산

| 연산 | 설명 | 예시 |
|------|------|------|
| `filter` | 조건에 맞는 요소만 | `{ it > 0 }` |
| `map` | 각 요소를 변환 | `{ it.uppercase() }` |
| `sortedBy` | 기준으로 정렬 | `{ it.length }` |
| `flatMap` | 각 요소를 **여러 개로 펼침** (1→N) | `{ it.bList }` |
| `flatten` | `List<List<T>>` → `List<T>` | — |
| `forEach` | 각 요소에 대해 실행 | `{ println(it) }` |

## `in` 연산자로 포함 여부 체크

```kotlin
val items = setOf("apple", "banana", "kiwifruit")
when {
    "orange" in items -> println("juicy")
    "apple" in items  -> println("apple is fine too")
}
```

## flatMap — 1 → N 변환

`map`은 요소 하나를 하나로 바꾸지만, `flatMap`은 요소 하나를 **여러 개(0개 포함)로 펼친다.**

```kotlin
class A(val bList: List<B>)

val allB: List<B> = aList.flatMap { it.bList }   // List<A> -> List<B>
```

`map`을 쓰면 중첩이 생겨 한 단계가 더 필요하다:

```kotlin
val nested: List<List<B>> = aList.map { it.bList }
val flat = nested.flatten()                       // flatMap = map + flatten
```

> [!warning] null 반환 금지
> 매퍼가 null을 돌려주면 안 된다. 펼칠 게 없으면 `emptyList()`를 반환한다.
> `aList.flatMap { it.bList ?: emptyList() }` → [[Null Safety]]

## Java Stream API와의 차이

Kotlin 컬렉션 연산은 Java Stream과 형태가 비슷하지만 **두 가지가 다르다.**

| | Kotlin Collection | [[Stream API\|Java Stream]] |
|---|---|---|
| `flatMap` 매퍼 반환 타입 | **`Iterable<R>`** | **`Stream<R>`** |
| 코드 | `flatMap { it.bList }` | `flatMap(a -> a.getBList().stream())` |
| 평가 시점 | **즉시(eager)** — 매 단계 새 List 생성 | 지연(lazy) |
| 지연 평가가 필요하면 | `.asSequence()` | 기본 지원 |

```kotlin
// 큰 컬렉션 + 여러 단계 + short-circuit이 필요할 때만 Sequence
val first = aList.asSequence()
    .flatMap { it.bList }
    .filter { it.isValid }
    .firstOrNull()
```

> [!important] Kotlin에서는 `.stream()`을 쓸 일이 거의 없다
> Java Stream API는 Java 라이브러리 상호작용이나 병렬 처리가 필요할 때만 쓴다. 평소엔 Kotlin 확장 함수가 관용적이다.

> [!failure] JPA 엔티티에 flatMap을 쓸 때 — N+1 주의
> ```kotlin
> val allItems = invoices.flatMap { it.items }   // items가 LAZY면 invoice 개수만큼 쿼리
> ```
> `flatMap`은 컬렉션을 순회하므로 LAZY 연관관계를 전부 초기화시킨다. `fetch join` 또는 `@BatchSize` 필요.

## 핵심 포인트

- `it`은 단일 파라미터 람다의 암시적 이름
- 체이닝으로 파이프라인 스타일 데이터 처리 가능
- Java의 [[Stream API]]와 유사하지만 더 간결하고, **기본이 즉시 평가**라는 점이 다름

## 관련 개념

- [[Kotlin]] — 언어 전체 개요
- [[Kotlin 제어 흐름]] — for 루프와 in 연산자
- [[Stream API]] — Java 대응 API, `flatMap` 시그니처 차이
- [[Null Safety]] — flatMap 매퍼의 null 처리

## 참고 소스

- [[Sources/ai-queries/2026-08-20_Stream-API와-flatMap]]
