[`Map<K, V>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/index.html) не является наследником интерфейса `Collection`; однако это один из типов коллекций в Kotlin. `Map` хранит пары "ключ-значение" (или _entries_); ключи уникальны, но разные ключи могут иметь одинаковые значения. Интерфейс `Map` предоставляет такие функции, как доступ к значению по ключу, поиск ключей и значений и т. д.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)

    println("All keys: ${numbersMap.keys}") // [key1, key2, key3, key4]
    println("All values: ${numbersMap.values}") // [1, 2, 3, 1]
    if ("key2" in numbersMap) println("Value by key \"key2\": ${numbersMap["key2"]}")    
    if (1 in numbersMap.values) println("The value 1 is in the map")
    if (numbersMap.containsValue(1)) println("The value 1 is in the map") // аналогичен предыдущему условию
}
```

Две `Map`-ы, содержащие равные пары, равны независимо от порядка пар.

```kotlin
fun main() {
    val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)    
    val anotherMap = mapOf("key2" to 2, "key1" to 1, "key4" to 1, "key3" to 3)

    println("The maps are equal: ${numbersMap == anotherMap}") // The maps are equal: true
}
```

[`MutableMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/index.html) - это `Map` с операциями записи, например, можно добавить новую пару "ключ-значение" или обновить значение, связанное с указанным ключом.

```kotlin
fun main() {
    val numbersMap = mutableMapOf("one" to 1, "two" to 2)
    numbersMap.put("three", 3)
    numbersMap["one"] = 11

    println(numbersMap) // {one=11, two=2, three=3}
}
```

По умолчанию реализацией `Map` является [`LinkedHashMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-map/index.html) - сохраняет порядок элементов. Альтернативная реализация - [`HashMap`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-map/index.html) - не сохраняет порядок элементов.