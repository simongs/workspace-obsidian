---
categories:
  - "[[Evergreen]]"
type: concept
domain:
  - Kotlin
related:
  - "[[Kotlin]]"
  - "[[Kotlin 제어 흐름]]"
sources:
  - "[[Kotlin Basic Syntax - kotlinlang.org]]"
created: 2026-04-12
updated: 2026-04-12
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
| `forEach` | 각 요소에 대해 실행 | `{ println(it) }` |

## `in` 연산자로 포함 여부 체크

```kotlin
val items = setOf("apple", "banana", "kiwifruit")
when {
    "orange" in items -> println("juicy")
    "apple" in items  -> println("apple is fine too")
}
```

## 핵심 포인트

- `it`은 단일 파라미터 람다의 암시적 이름
- 체이닝으로 파이프라인 스타일 데이터 처리 가능
- Java의 Stream API와 유사하지만 더 간결

## 관련 개념

- [[Kotlin]] — 언어 전체 개요
- [[Kotlin 제어 흐름]] — for 루프와 in 연산자
