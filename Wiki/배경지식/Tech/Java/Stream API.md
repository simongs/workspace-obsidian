---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Java
related:
  - "[[Kotlin 컬렉션]]"
  - "[[Null Safety]]"
sources:
  - "[[Sources/ai-queries/2026-08-20_Stream-API와-flatMap]]"
created: 2026-08-20
updated: 2026-08-20
tags:
  - 0🌲
  - wiki
  - java
  - stream
---

## 정의

**Stream API**는 Java 8에서 도입된, 데이터 시퀀스를 선언적으로 처리하는 파이프라인 추상이다.

컬렉션 순회가 가장 흔한 용례지만 Stream의 본질은 그보다 넓다:

| 흔한 오해 | 실제 |
|---|---|
| 컬렉션에서만 만든다 | `Files.lines()`, `Stream.iterate()`, `Random.ints()` 등 — **무한 스트림도 가능** |
| 순차 처리다 | `parallelStream()`으로 병렬 가능 |
| 순회 도구다 | 핵심 성질은 **지연 평가(lazy)** |

## 연산의 두 종류

| 종류 | 예 | 특징 |
|------|----|----|
| **중간 연산** | `filter`, `map`, `flatMap`, `sorted`, `distinct` | `Stream`을 반환. **아무것도 실행하지 않음** |
| **최종 연산** | `toList`, `forEach`, `reduce`, `count`, `findFirst` | 파이프라인을 실제로 구동. 스트림 소비 후 재사용 불가 |

> [!insight] 지연 평가가 왜 중요한가
> `list.stream().filter(...).map(...).toList()`는 **리스트를 두 번 순회하지 않는다.** 한 번 순회하면서 각 요소마다 filter→map을 통과시킨다.
> 그래서 중간 리스트가 메모리에 생기지 않고, `findFirst()`를 붙이면 조건을 만족하는 첫 요소를 찾자마자 순회를 멈춘다(short-circuit).
> "스트림이 for문보다 느릴 것"이라는 오해가 여기서 갈린다.

## 주요 연산 대조

| 연산 | 변환 | 매퍼 반환 (Java) | 매퍼 반환 (Kotlin) |
|---|---|---|---|
| `filter` | 1 → 0 or 1 | `boolean` (`Predicate`) | `Boolean` |
| `map` | 1 → 1 | `R` | `R` |
| **`flatMap`** | **1 → N** | **`Stream<R>`** | **`Iterable<R>`** |

## flatMap — map은 1→1, flatMap은 1→N

`map`은 요소 하나를 요소 하나로 바꾼다. `flatMap`은 요소 하나를 **여러 개(0개 포함)로 펼친다.**

> [!example] 평탄화 과정
> `List<A>` → `[ a1, a2, a3 ]`
> ↓ `map(a -> a.getBList())`
> `Stream<List<B>>` → `[ [b1,b2], [], [b3] ]` ← **중첩됨**
> ↓ `flatMap(a -> a.getBList().stream())`
> `Stream<B>` → `[ b1, b2, b3 ]` ← **평탄화됨**

즉 **`flatMap` = `map` + `flatten`**. 이름 그대로다.

```java
// ✅ Java — 매퍼가 Stream을 반환해야 함
List<B> result = aList.stream()
        .flatMap(a -> a.getBList().stream())
        .toList();

// ❌ 컴파일 에러 — List<B>는 Stream이 아님
.flatMap(a -> a.getBList())
```

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)
//                                          ^^^^^^^^^^^^^^^^ Stream 필수
```

### 왜 Collection이 아니라 Stream을 요구하는가

`flatMap`은 Stream만의 메서드가 아니며, 모든 구현이 같은 규칙을 따른다.

| 대상 | 시그니처 | 매퍼 반환 타입 |
|---|---|---|
| `Stream<T>` | `flatMap(T -> Stream<R>)` | Stream |
| `Optional<T>` | `flatMap(T -> Optional<R>)` | Optional |
| `CompletableFuture<T>` | `thenCompose(T -> CompletableFuture<R>)` | CompletableFuture |

> [!important] flatMap의 규칙
> **매퍼는 자기 자신과 같은 컨테이너 타입을 반환해야 한다.**
> `Collection`을 허용하면 이 일관성이 깨지고, 컬렉션이 아닌 소스(파일 라인, 무한 스트림)를 펼치는 경우를 표현할 수 없다.
>
> ```java
> paths.stream()
>      .flatMap(p -> Files.lines(p))   // 파일의 각 줄로 펼침 — 컬렉션 아님
>      .toList();
> ```

> [!insight] 모나드의 bind 연산
> 이 패턴에는 이름이 있다 — **모나드(monad)의 bind**. 실용적 의미는 단순하다: **"중첩을 만들지 않고 연쇄시키는 방법."**
> `Optional<Optional<T>>`, `Stream<Stream<T>>`, `Future<Future<T>>` 같은 중첩은 다루기가 지옥인데, `flatMap`은 그 중첩이 생기기 전에 한 겹을 벗긴다.
> **map을 쓰면 중첩이 쌓이고 flatMap을 쓰면 평탄하게 유지된다** — 이 한 문장이 Java·Kotlin·JS(`Promise.then`)·Rust(`and_then`)에 전부 통한다.

## Java vs Kotlin

| | Java Stream | Kotlin Collection |
|---|---|---|
| 매퍼 반환 타입 | `Stream<R>` | `Iterable<R>` |
| 코드 | `flatMap(a -> a.getBList().stream())` | `flatMap { it.bList }` |
| 평가 시점 | 지연(lazy) | **즉시(eager)** — 매 단계 새 List 생성 |
| 지연이 필요하면 | 기본 지원 | `.asSequence()` |

```kotlin
public inline fun <T, R> Iterable<T>.flatMap(transform: (T) -> Iterable<R>): List<R>
//                                                              ^^^^^^^^ Iterable이면 충분
```

```kotlin
// 큰 컬렉션 + 여러 단계 + short-circuit이 필요할 때만 Sequence
val first = aList.asSequence()
    .flatMap { it.bList }
    .filter { it.isValid }
    .firstOrNull()
```

> [!important] Kotlin 환경에서는 `.stream()`을 쓸 일이 거의 없다
> Java Stream API는 Java 라이브러리와 상호작용하거나 병렬 처리가 필요할 때만 쓰고, 평소엔 [[Kotlin 컬렉션]]의 확장 함수를 쓰는 것이 관용적이다.

## toList()의 함정

```java
List<String> a = stream.toList();                      // Java 16+ — 불변(unmodifiable)
List<String> b = stream.collect(Collectors.toList());  // 가변 (구현상 ArrayList)
```

> [!warning] `Stream.toList()`는 ArrayList가 아니다
> ```java
> List<B> result = aList.stream().map(...).toList();
> result.add(newB);   // 💥 UnsupportedOperationException
> ```
> 가변 리스트가 필요하면 `Collectors.toCollection(ArrayList::new)` 또는 `new ArrayList<>(stream.toList())`.
> 참고: `Collectors.toList()`가 `ArrayList`를 반환하는 것은 **문서화된 보장이 아니라 구현 세부사항**이다. 보장되는 건 "가변"까지다.

## 자주 밟는 함정

**1) 매퍼에서 null 반환 금지**

```java
.flatMap(a -> a.getBList() == null ? null : a.getBList().stream())  // 💥 NPE
.flatMap(a -> a.getBList() == null ? Stream.empty() : a.getBList().stream())  // ✅
```

`flatMap`은 0개로 펼치는 것도 정상 동작이므로 **빈 스트림**을 돌려주면 된다. Kotlin이라면 `flatMap { it.bList ?: emptyList() }`. → [[Null Safety]]

**2) Optional 펼치기 (Java 9+)**

```java
list.stream()
    .map(this::findB)          // Stream<Optional<B>>
    .flatMap(Optional::stream) // Stream<B> — 빈 Optional은 자동 제거
    .toList();
```

**3) JPA에서 flatMap = N+1 쿼리 폭탄**

```kotlin
val allItems = invoices.flatMap { it.items }   // items가 LAZY면 invoice 개수만큼 쿼리
```

> [!failure] 타입 시그니처가 성능 특성을 숨긴다
> `flatMap`의 N+1은 스트림 API 자체의 문제가 아니라 **"컬렉션처럼 보이지만 실은 DB 접근인 것"** 을 다루기 때문이다. Hibernate의 `PersistentBag`은 `List` 인터페이스를 구현하지만 첫 접근 시 쿼리를 날린다.
> JPA 엔티티를 스트림으로 다룰 땐 항상 **"이 컬렉션은 이미 로딩됐나?"** 를 먼저 물어야 한다. `fetch join` 또는 `@BatchSize`로 대응.

## 정산 도메인 적용

```kotlin
// Invoice 여러 개에서 모든 InvoiceItem을 뽑아 합계
val totalAmount = invoices
    .flatMap { it.items }
    .filter { it.status == POSTED }
    .sumOf { it.amount }

// 전표에서 모든 전표라인 뽑기
val allLines = sapSlips.flatMap { it.lines }
```

`flatMap` 없이 쓰면 중첩이 생기고 한 단계가 더 필요하다:

```kotlin
val nested: List<List<InvoiceItem>> = invoices.map { it.items }
val flat = nested.flatten()
```

## 관련 개념

- [[Kotlin 컬렉션]] — Kotlin의 대응 연산 (eager, `Iterable` 기반)
- [[Null Safety]] — flatMap 매퍼의 null 처리

## 참고 소스

- [[Sources/ai-queries/2026-08-20_Stream-API와-flatMap]]
