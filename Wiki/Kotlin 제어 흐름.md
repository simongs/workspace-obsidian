---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Kotlin
related:
  - "[[Kotlin]]"
  - "[[Kotlin 함수]]"
  - "[[Kotlin 컬렉션]]"
sources:
  - "[[Kotlin Basic Syntax - kotlinlang.org]]"
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

Kotlin의 제어 흐름은 Java와 비슷하지만, 많은 구문이 **문(statement)**이 아닌 **식(expression)**으로 동작한다.

## if 표현식

Java에서 `if`는 문이지만, Kotlin에서는 **값을 반환하는 식**이다:

```kotlin
// 문(statement)으로 사용
if (a > b) { print("a") } else { print("b") }

// 식(expression)으로 사용 — 값 반환
val max = if (a > b) a else b
```

> [!tip] 삼항 연산자가 없는 이유
> Kotlin에는 `? :` 삼항 연산자가 없다. `if`가 이미 식이므로 불필요하다.

## when 표현식

Java의 `switch`를 대체하는 강력한 패턴 매칭:

```kotlin
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
```

- 값 매칭, 타입 체크(`is`), 범위 체크(`in`) 모두 가능
- `else`는 `switch`의 `default`에 해당

## for 루프

```kotlin
// 컬렉션 순회
for (item in items) { println(item) }

// 인덱스 순회
for (index in items.indices) { println("item at $index is ${items[index]}") }
```

## Ranges (범위)

```kotlin
if (x in 1..10) { }           // 범위 체크
for (x in 1..5) { print(x) }  // 1, 2, 3, 4, 5
for (x in 1..10 step 2) { }   // 1, 3, 5, 7, 9
for (x in 9 downTo 0 step 3) { } // 9, 6, 3, 0
```

- `in` 연산자로 범위 소속 여부 체크
- `step`, `downTo`로 진행 방향/간격 제어

## 관련 개념

- [[Kotlin]] — 언어 전체 개요
- [[Kotlin 함수]] — 표현식 본문과 제어 흐름의 결합
- [[Kotlin 컬렉션]] — for 루프와 함수형 연산
