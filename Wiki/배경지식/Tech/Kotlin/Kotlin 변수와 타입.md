---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Kotlin
related:
  - "[[Kotlin]]"
  - "[[Kotlin 함수]]"
  - "[[Null Safety]]"
sources:
  - "[[Kotlin Basic Syntax - kotlinlang.org]]"
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 정의

Kotlin의 변수 시스템은 **불변성(immutability)**을 기본 권장하며, 강력한 **타입 추론**을 제공한다.

## val vs var

| 키워드 | 의미 | 재할당 | Java 비유 |
|--------|------|--------|-----------|
| `val` | 읽기 전용 (immutable) | 불가 | `final` 변수 |
| `var` | 가변 (mutable) | 가능 | 일반 변수 |

```kotlin
val x: Int = 5    // 재할당 불가
var y: Int = 5    // 재할당 가능
y += 1            // OK
// x += 1         // 컴파일 오류!
```

> [!tip] Kotlin스러운 코드
> 기본적으로 `val`을 사용하고, 꼭 필요할 때만 `var`을 쓴다. 불변성을 기본으로 하면 버그가 줄어든다.

## 타입 추론 (Type Inference)

```kotlin
val x = 5         // Int로 자동 추론
val name = "Kotlin"  // String으로 자동 추론
```

- 타입을 명시하지 않아도 컴파일러가 추론
- 선언과 초기화를 분리할 때는 타입 명시 필수:

```kotlin
val c: Int     // 타입 명시 필수
c = 3          // 나중에 초기화
```

## 스마트 캐스트 (Smart Cast)

`is` 연산자로 타입을 체크하면, 해당 블록 안에서 **자동으로 캐스팅**된다:

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        return obj.length  // String으로 자동 캐스트
    }
    return null
}
```

- 명시적 캐스팅(`as`) 불필요
- [[Null Safety]]의 null 체크 후 자동 캐스트와 동일한 원리

## 관련 개념

- [[Kotlin]] — 언어 전체 개요
- [[Null Safety]] — nullable 타입과 스마트 캐스트
- [[Kotlin 함수]] — 표현식 본문과 타입 추론
