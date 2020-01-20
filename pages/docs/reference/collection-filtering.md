---
type: doc
layout: reference
category: "Collections"
title: "过滤集合"
---

# 过滤

过滤是最常用的集合操作之一。在Kotlin中，过滤条件由 _谓词_ 定义， _谓词_ 是一个接受一个集合元素并且返回一个布尔值的lambda表达式：返回 `true` 说明这个元素匹配谓词条件，返回 `false` 说明不匹配。

标准库包含了一组让你能够通过单个调用就可以过滤集合的扩展函数。这些函数不会改变原始集合，因此[可变和只读的](collections-overview.html#集合类型)集合都可以访问它们。为了操作过滤结果，你应该把过滤后的集合赋值给一个变量或者在过滤之后继续调用其他函数。

## 按谓词过滤

基础的过滤函数是[`filter()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html)。当使用一个谓词来调用时，`filter()` 返回匹配谓词的集合元素。对于 `List` 和` Set` 对象，过滤结果都是一个 `List` 对象，对 `Map` 对象来说结果还是一个 `Map` 对象。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")  
    val longerThan3 = numbers.filter { it.length > 3 }
    println(longerThan3)

    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
    val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
    println(filteredMap)
//sampleEnd
}
```
</div>

`filter()`函数中的谓词只能检查元素的值。如果你想使用元素在集合中的位置，应该使用[`filterIndexed()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-indexed.html)函数。它接受一个带有两个参数的谓词：元素的下标和元素的值。

如果想使用否定条件来过滤集合，请使用[`filterNot()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not.html)函数。它返回一个让谓词返回 `false` 的元素列表。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    
    val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5)  }
    val filteredNot = numbers.filterNot { it.length <= 3 }

    println(filteredIdx)
    println(filteredNot)
//sampleEnd
}
```
</div>

还有一些函数能够通过过滤给定类型的元素来缩小元素的类型：

* [`filterIsInstance()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html) 返回给定类型的集合元素。由一个 `List<Any>` 类型的对象所调用，`filterIsInstance<T>()` 函数返回一个 `List<T>` 对象，从而允许你在集合元素上调用 `T` 类型对象的函数。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf(null, 1, "two", 3.0, "four")
    println("All String elements in upper case:")
    numbers.filterIsInstance<String>().forEach {
        println(it.toUpperCase())
    }
//sampleEnd
}
```
</div>

* [`filterNotNull()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html) 函数返回所有的非空元素。由一个 `List<T?>`类型的对象所调用，`filterNotNull()` 函数返回一个 `List<T: Any>` 对象，从而允许你将所有元素视为非空对象。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf(null, "one", "two", null)
    numbers.filterNotNull().forEach {
        println(it.length)   // length is unavailable for nullable Strings
    }
//sampleEnd
}
```
</div>

## 划分

另一个过滤函数– [`partition()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/partition.html) – 通过一个谓词过滤集合并且将不匹配的元素存放在一个单独的列表中。因此，你得到一个 `List` 的 `Pair` 作为返回值：第一个列表包含匹配谓词的元素并且第二个列表包含原始集合中的所有其他元素。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val (match, rest) = numbers.partition { it.length > 3 }

    println(match)
    println(rest)
//sampleEnd
}
```
</div>

## 检验谓词

最后，有些函数只是针对集合元素简单地检测一个谓词：

* [`any()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html) 返回 `true` 如果至少有一个元素匹配谓词。
* [`none()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) 返回 `true` 如果没有元素匹配谓词。
* [`all()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/all.html) 返回 `true` 如果所有元素都匹配谓词。注意，在一个空集合上使用任何有效的谓词去调用 `all()` 函数都会返回 `true` 。这种行为在逻辑上被称为 [_vacuous truth_](https://en.wikipedia.org/wiki/Vacuous_truth)。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")

    println(numbers.any { it.endsWith("e") })
    println(numbers.none { it.endsWith("a") })
    println(numbers.all { it.endsWith("e") })

    println(emptyList<Int>().all { it > 5 })   // vacuous truth
//sampleEnd
}
```
</div>

`any()` 和 `none()` 也可以不带谓词使用：在这种情况下它们只是用来检查集合是否为空。
如果集合中有元素，`any()` 返回 `true` ，如果没有则返回 `false`；`none()` 与之相反。

<div class="sample" markdown="1" theme="idea" data-min-compiler-version="1.3">

```kotlin
fun main() {
//sampleStart
    val numbers = listOf("one", "two", "three", "four")
    val empty = emptyList<String>()

    println(numbers.any())
    println(empty.any())
    
    println(numbers.none())
    println(empty.none())
//sampleEnd
}
```
</div>

