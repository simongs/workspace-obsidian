---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Kotlin
related:
  - "[[Kotlin]]"
  - "[[Kotlin 변수와 타입]]"
sources:
  - "[[Kotlin Basic Syntax - kotlinlang.org]]"
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

Null Safety는 Kotlin의 타입 시스템에 내장된 null 참조 방지 기능이다. **NullPointerException(NPE)**을 컴파일 타임에 잡아준다.

## Nullable 타입

타입 이름 뒤에 `?`를 붙이면 nullable:

```kotlin
val name: String = "Kotlin"    // null 불가
val name: String? = null       // null 가능
```

| 타입 | null 허용 | 예시 |
|------|-----------|------|
| `String` | 불가 | `"hello"` |
| `String?` | 가능 | `"hello"` 또는 `null` |
| `Int` | 불가 | `42` |
| `Int?` | 가능 | `42` 또는 `null` |

## Null 체크 후 스마트 캐스트

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}

val x = parseInt("6")
val y = parseInt("a")

if (x != null && y != null) {
    println(x * y)   // 여기서 x, y는 자동으로 Int (non-null)
}
```

> [!important] 핵심 원리
> null 체크를 한 블록 안에서는 컴파일러가 자동으로 non-nullable 타입으로 캐스팅한다. 이것이 [[Kotlin 변수와 타입]]의 **스마트 캐스트**와 동일한 원리다.

## 왜 중요한가

- Java에서 가장 흔한 런타임 오류가 NPE
- Kotlin은 이를 **컴파일 타임**에 잡아줌
- `?`를 쓰지 않으면 null이 될 수 없음을 타입 시스템이 보장

## 관련 개념

- [[Kotlin]] — 언어 전체 개요
- [[Kotlin 변수와 타입]] — 스마트 캐스트 메커니즘
