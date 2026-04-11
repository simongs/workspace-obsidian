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

Kotlin에서 함수는 `fun` 키워드로 선언한다.

## 함수 선언 형태

### 기본 형태 — 블록 본문 (Block body)

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

- 반환 타입을 명시적으로 선언
- `return` 키워드로 값 반환

### 표현식 본문 (Expression body)

```kotlin
fun sum(a: Int, b: Int) = a + b
```

- `=` 뒤에 표현식을 바로 작성
- 반환 타입이 자동 추론됨
- 한 줄로 간결하게 표현 가능

> [!tip] Kotlin스러운 코드
> 함수 본문이 단일 표현식이면 표현식 본문을 사용하는 것이 관용적(idiomatic)이다.

### Unit 반환 — 반환값 없는 함수

```kotlin
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
```

- `Unit`은 Java의 `void`에 해당
- `Unit` 타입은 생략 가능

## 핵심 포인트

- Kotlin은 **문(statement)**과 **식(expression)**의 구분이 중요하다
- `if`, `when`도 식으로 사용 가능 → 함수 표현식 본문과 자연스럽게 결합

## 관련 개념

- [[Kotlin]] — 언어 전체 개요
- [[Kotlin 변수와 타입]] — `val`/`var`와 타입 추론
- [[Kotlin 제어 흐름]] — `if` 표현식, `when` 표현식
