---
categories:
  - "[[Evergreen]]"
type: entity
domain:
  - Kotlin
related:
  - "[[JetBrains]]"
  - "[[JVM]]"
  - "[[Null Safety]]"
  - "[[Kotlin 함수]]"
  - "[[Kotlin 변수와 타입]]"
sources:
  - "[[Kotlin Basic Syntax - kotlinlang.org]]"
  - "[[Kotlin Getting Started - kotlinlang.org]]"
created: 2026-04-12
updated: 2026-04-12
tags:
  - 0🌲
  - wiki
---

## 개요

Kotlin은 [[JetBrains]]가 개발한 현대적 프로그래밍 언어다. 간결하고(concise), 멀티플랫폼이며, Java와 완전히 상호운용(interoperable)된다.

## 핵심 특징

- **간결함** — 보일러플레이트 코드를 크게 줄여줌
- **[[Null Safety]]** — 타입 시스템 수준에서 null 참조 오류 방지
- **타입 추론** — 컴파일러가 변수 타입을 자동 추론
- **함수형 프로그래밍** — 람다, 고차 함수, 컬렉션 연산 지원
- **Java 상호운용** — 기존 Java 코드와 100% 호환
- **멀티플랫폼** — JVM, Android, iOS, JS, Native 모두 지원

## 사용 영역

| 영역 | 도구/프레임워크 | 비고 |
|------|----------------|------|
| 백엔드 | Spring Boot, Ktor | Java 프로젝트에 점진적 도입 가능 |
| Android | Android Studio | Google 공식 권장 언어 |
| 멀티플랫폼 | Kotlin Multiplatform, Compose | iOS/Android 코드 공유 |
| 데이터 사이언스 | Kotlin Notebook, DataFrame, Kandy | 탐색적 분석에 활용 |

## 설치

[[IntelliJ IDEA]] 또는 [[Android Studio]]에 기본 포함.

## 관련 개념

- [[Kotlin 함수]] — 함수 선언, 표현식 본문, Unit 타입
- [[Kotlin 변수와 타입]] — val/var, 타입 추론, 스마트 캐스트
- [[Null Safety]] — nullable 타입, 안전 호출, 스마트 캐스트
- [[Kotlin 컬렉션]] — filter, map, sortedBy 등 함수형 연산
- [[Kotlin 제어 흐름]] — if 표현식, when, for, while, ranges
