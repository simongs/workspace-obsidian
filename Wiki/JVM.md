---
categories:
  - "[[Evergreen]]"
type: entity
domain:
  - Kotlin
related:
  - "[[Kotlin]]"
  - "[[JetBrains]]"
sources:
  - "[[Kotlin Getting Started - kotlinlang.org]]"
created: 2026-04-18
updated: 2026-04-18
tags:
  - 0🌲
  - wiki
---

## 개요

JVM(Java Virtual Machine)은 Java 바이트코드를 실행하는 런타임 환경이다. 원래 Java용으로 설계되었지만, 현재는 [[Kotlin]], Scala, Groovy, Clojure 등 다수의 언어가 JVM 위에서 동작한다.

## Kotlin과의 관계

- Kotlin의 기본 타겟 플랫폼 — `.kt` 파일이 `.class` 바이트코드로 컴파일됨
- Java와 **100% 상호운용** 가능 — 동일 프로젝트에서 Kotlin 클래스가 Java 클래스를 호출하거나 반대도 가능
- 기존 Java 생태계(Spring, Gradle, Maven 등)를 그대로 사용

## Kotlin의 멀티플랫폼 타겟

[[Kotlin]]은 JVM 외에도 여러 런타임을 지원한다:

| 타겟 | 용도 |
|------|------|
| **JVM** | 백엔드, 데스크톱, Android(Dalvik/ART) |
| **JS** | 웹 프론트엔드 |
| **Native** | iOS, 임베디드 |
| **Wasm** | 브라우저 고성능 실행 |

## 관련 항목

- [[Kotlin]] — JVM을 주 타겟으로 하는 언어
- [[JetBrains]] — Kotlin/JVM 컴파일러 개발사
