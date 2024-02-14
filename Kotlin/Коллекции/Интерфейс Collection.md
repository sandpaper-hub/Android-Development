[`Collection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html) является корнем в иерархии коллекций. Этот интерфейс представляет собой обычное поведение неизменяемой коллекции: операции типа `size`, `get` и т. д. `Collection` наследуется от интерфейса `Iterable<T>`, который определяет операции для итерации элементов. Вы можете использовать `Collection` как параметр функции, которая может работать с разными типами коллекций. Для более конкретных случаев следует использовать наследников `Collection`: [`List`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html) и [`Set`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html).

```kotlin
fun printAll(strings: Collection<String>) {
        for(s in strings) print("$s ")
        println()
    }

fun main() {
    val stringList = listOf("one", "two", "one")
    printAll(stringList) // one two one

    val stringSet = setOf("one", "two", "three")
    printAll(stringSet) // one two three
}
```

[`MutableCollection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-collection/index.html) - это `Collection` с операциями записи, такими как `add` и `remove`.

```kotlin
fun List<String>.getShortWordsTo(shortWords: MutableList<String>, maxLength: Int) {
    this.filterTo(shortWords) { it.length <= maxLength }
    // throwing away the articles
    val articles = setOf("a", "A", "an", "An", "the", "The")
    shortWords -= articles
}

fun main() {
    val words = "A long time ago in a galaxy far far away".split(" ")
    val shortWords = mutableListOf<String>()
    words.getShortWordsTo(shortWords, 3)
    println(shortWords) // [ago, in, far, far]
}
```

[[List и MutableList]]
[[Set и MutableSet]]
[[Map и MutableMap]]