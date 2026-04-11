---
categories:
  - "[[Clippings]]"
tags:
  - clippings
  - kotlin
author:
  - "[[JetBrains]]"
url: "https://kotlinlang.org/docs/basic-syntax.html"
created: 2026-04-12
published:
topics:
  - "[[Kotlin]]"
domain:
  - Kotlin
---

## Basic syntax overview

This is a collection of basic syntax elements with examples. At the end of every section, you'll find a link to a detailed description of the related topic.

## Package definition and imports

Package specification should be at the top of the source file:

```kotlin
package my.demo
import kotlin.text.*
```

It is not required to match directories and packages: source files can be placed arbitrarily in the file system.

## Program entry point

An entry point of a Kotlin application is the `main` function:

```kotlin
fun main() {
    println("Hello world!")
}
```

Another form of `main` accepts a variable number of `String` arguments:

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```

## Functions

A function with two `Int` parameters and `Int` return type:

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

A function body can be an expression. Its return type is inferred:

```kotlin
fun sum(a: Int, b: Int) = a + b
```

A function that returns no meaningful value:

```kotlin
fun printSum(a: Int, b: Int): Unit {
    println("sum of $a and $b is ${a + b}")
}
```

`Unit` return type can be omitted.

## Variables

- `val` — immutable, read-only (assigned once)
- `var` — mutable (can be reassigned)

```kotlin
val x: Int = 5       // immutable
var y: Int = 5       // mutable
y += 1

val z = 5            // type inference
```

Variables can be declared at the top level.

## Classes and instances

```kotlin
class Shape

class Rectangle(val height: Double, val length: Double) {
    val perimeter = (height + length) * 2
}
```

- Classes are `final` by default; use `open` to make inheritable
- Default constructor with parameters is available automatically

## String templates

```kotlin
var a = 1
val s1 = "a is $a"
a = 2
val s2 = "${s1.replace("is", "was")}, but now is $a"
```

## Conditional expressions

`if` can be used as an expression:

```kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

## when expression

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

## Ranges

```kotlin
if (x in 1..y+1) { println("fits in range") }

for (x in 1..5) { print(x) }
for (x in 1..10 step 2) { print(x) }
for (x in 9 downTo 0 step 3) { print(x) }
```

## Collections

```kotlin
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
fruits
    .filter { it.startsWith("a") }
    .sortedBy { it }
    .map { it.uppercase() }
    .forEach { println(it) }
```

## Nullable values and null checks

Nullable type names have `?` at the end:

```kotlin
fun parseInt(str: String): Int? {
    return str.toIntOrNull()
}
```

After null check, variables are automatically cast to non-nullable.

## Type checks and automatic casts

The `is` operator checks type, and automatic smart cast applies:

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        return obj.length  // automatically cast to String
    }
    return null
}
```
