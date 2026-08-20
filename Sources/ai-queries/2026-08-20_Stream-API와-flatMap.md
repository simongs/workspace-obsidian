---
created: 2026-08-20
tags:
  - java
  - kotlin
  - stream
  - query
source-type: ai-query
wiki:
  - "[[Wiki/배경지식/Tech/Java/Stream API]]"
  - "[[Wiki/배경지식/Tech/Kotlin/Kotlin 컬렉션]]"
---

# Stream API와 flatMap — Query Session

## 세션 정보

- 날짜: 2026-08-20
- 교환 횟수: 1회 (사용자의 자가 정리 + 검증 요청)
- 주제: Java Stream API의 `filter`/`map`/`toList`/`flatMap`, Kotlin 컬렉션과의 대비
- 참조한 Wiki 페이지: [[Wiki/배경지식/Tech/Kotlin/Kotlin 컬렉션]]
- 진행 방식: 사용자가 자기 이해를 먼저 서술하고 Claude가 검증 → **채점 형태**로 정정

---

## Q&A 원문

### Q. stream 그리고 flatMap 한번 정리하고 갈게

**사용자의 자가 정리 (원문 요지)**

- stream = 컬렉션을 순차적으로 탐색할 수 있는 것
- `filter` = `Predicate` 인터페이스로 item이 boolean 판정되어 결과 목록 포함 여부 결정
- `map` = A → B 로 transforming
- `toList()` = 최종적으로 **ArrayList** 형태로 결과가 나옴
- `flatMap` = flat + map. 펼쳐질 수 있는 무언가(= 컬렉션)를 모두 펼쳐서 다시 스트림으로 만드는 것

**사용자의 질문**: 객체 A가 `List<B>`를 들고 있고 `List<A>`를 연산 중일 때

1. `aList.stream().flatMap(a -> a.getBList().stream()).toList()`
2. `flatMap(a -> a.getBList()).toList()`

중 어느 쪽인가? **"내가 보기엔 후자가 더 맞는 거 같은데"**

---

## A. 채점

| 항목 | 판정 |
|---|---|
| Stream = 순차 탐색 | ◐ 대체로 맞지만 좁음 |
| `filter` = `Predicate`로 boolean 판정 | ✅ 정확 |
| `map` = A → B 변환 | ✅ 정확 |
| `toList()` = ArrayList | ❌ **틀림** |
| flatMap = 컬렉션을 펼쳐 다시 스트림으로 | ✅ **개념은 정확** |
| **최종 추측: 후자가 맞다** | ❌ **Java에서는 틀림 — 전자가 정답** |

> [!warning] 사실 정정 1 — `toList()`는 ArrayList가 아니다
> Java 16부터의 `Stream.toList()`는 **불변(unmodifiable) 리스트**를 반환한다. `add()` 시 `UnsupportedOperationException`.
> 가변 리스트가 필요하면 `Collectors.toList()`(ArrayList, 단 문서화된 보장은 "가변"까지만) 또는 `Collectors.toCollection(ArrayList::new)`.

> [!warning] 사실 정정 2 — Java `flatMap`은 Stream을 요구한다
> ```java
> <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)
> ```
> 매퍼가 `Stream`을 반환해야 하므로 `.stream()`이 필수. `flatMap(a -> a.getBList())`는 컴파일 에러.

> [!info] Stream 정의 보강
> - 출처가 컬렉션이 아니어도 된다 (`Files.lines()`, `Stream.iterate()`, 무한 스트림)
> - 순차가 아닐 수도 있다 (`parallelStream()`)
> - 핵심 성질은 **지연 평가(lazy)** — 중간 연산은 아무것도 실행하지 않고, 최종 연산 시점에 **한 번의 순회**로 전부 처리

---

## 핵심 개념: map은 1→1, flatMap은 1→N

> [!example] 평탄화 과정
> `List<A>` → `[ a1, a2, a3 ]`
> `map(a -> a.getBList())` → `Stream<List<B>>` = `[ [b1,b2], [], [b3] ]` ← **중첩**
> `flatMap(...)` → `Stream<B>` = `[ b1, b2, b3 ]` ← **평탄화**

`map`을 쓰면 `Stream<List<B>>`라는 중첩 구조가 나온다. `flatMap`은 껍질을 한 겹 벗겨 `Stream<B>`로 만든다. 즉 **`flatMap` = `map` + `flatten`** — 사용자의 "flat + map" 해석이 정확했다.

## 왜 Java는 굳이 Stream을 요구하는가

`flatMap`은 Stream만의 메서드가 아니며, 모든 구현이 **같은 규칙**을 따른다.

| 대상 | 시그니처 | 매퍼 반환 타입 |
|---|---|---|
| `Stream<T>` | `flatMap(T -> Stream<R>)` | Stream |
| `Optional<T>` | `flatMap(T -> Optional<R>)` | Optional |
| `CompletableFuture<T>` | `thenCompose(T -> CompletableFuture<R>)` | CompletableFuture |

> **규칙: `flatMap`의 매퍼는 자기 자신과 같은 컨테이너 타입을 반환해야 한다.**

`Collection`을 허용하면 이 일관성이 깨지고, 컬렉션이 아닌 소스(파일 라인, 무한 스트림)를 펼치는 경우를 표현할 수 없다.

> [!insight] 모나드의 bind
> 이 패턴의 이름은 **모나드(monad)의 bind 연산**. 실용적 의미는 **"중첩을 만들지 않고 연쇄시키는 방법"**.
> `Optional<Optional<T>>`, `Stream<Stream<T>>`, `Future<Future<T>>` 같은 중첩은 다루기 지옥인데 `flatMap`은 중첩이 생기기 전에 한 겹을 벗긴다.
> **map을 쓰면 중첩이 쌓이고 flatMap을 쓰면 평탄하게 유지된다** — 이 한 문장이 Java·Kotlin·JS(`Promise.then`)·Rust(`and_then`)에 전부 통한다.

---

## 사용자 추측이 맞는 경우: Kotlin

```kotlin
public inline fun <T, R> Iterable<T>.flatMap(transform: (T) -> Iterable<R>): List<R>
```

Kotlin은 `Iterable`을 그대로 받으므로 `.stream()`이 불필요하다. **사용자가 추측한 후자 형태가 Kotlin에서는 정답이다.**

| | Java Stream | Kotlin Collection |
|---|---|---|
| 매퍼 반환 타입 | `Stream<R>` | `Iterable<R>` |
| 코드 | `flatMap(a -> a.getBList().stream())` | `flatMap { it.bList }` |
| 평가 시점 | 지연(lazy) | **즉시(eager)** — 매 단계 새 List 생성 |
| 지연이 필요하면 | 기본 지원 | `.asSequence()` |

---

## 자주 밟는 함정 3개

1. **매퍼에서 null 반환 금지** — `Stream.empty()` / `emptyList()`를 반환할 것. 0개로 펼치는 것은 정상 동작
2. **`Optional`을 펼칠 때 (Java 9+)** — `.flatMap(Optional::stream)`으로 빈 Optional 자동 제거
3. **JPA에서 flatMap = N+1 쿼리 폭탄** — `flatMap`은 컬렉션을 순회하므로 LAZY 연관관계를 전부 초기화시킨다. `fetch join`이나 `@BatchSize` 필요

> [!insight] 타입 시그니처가 성능 특성을 숨긴다
> `flatMap`의 N+1은 스트림 API 자체의 문제가 아니라 **"컬렉션처럼 보이지만 실은 DB 접근인 것"** 을 다루기 때문이다. Hibernate의 `PersistentBag`은 `List` 인터페이스를 구현하지만 첫 접근 시 쿼리를 날린다.
> JPA 엔티티를 스트림으로 다룰 땐 항상 "이 컬렉션은 이미 로딩됐나?"를 먼저 물어야 한다.

---

## 최종 답

- **Java**: `aList.stream().flatMap(a -> a.getBList().stream()).toList()` ← **전자**
- **Kotlin**: `aList.flatMap { it.bList }` ← 사용자가 추측한 **후자** 형태

개념 해석("컬렉션을 펼쳐 다시 스트림으로")은 완전히 정확했고, **언어별 API 설계 차이만 갈렸다.**
