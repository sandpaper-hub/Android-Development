[`Set<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html) хранит уникальные элементы; их порядок обычно не определён. `null` также является уникальным элементом: `Set` может содержать только один `null`. Два множества равны, если они имеют одинаковый размер и для каждого элемента множества есть равный элемент в другом множестве.

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3, 4)
    println("Number of elements: ${numbers.size}") // Number of elements: 4
    if (numbers.contains(1)) println("1 is in the set")

    val numbersBackwards = setOf(4, 3, 2, 1)
    println("The sets are equal: ${numbers == numbersBackwards}") // true
}
```

[`MutableSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/index.html) - это `Set` с операциями записи из `MutableCollection`.

По умолчанию реализацией `Set` является [`LinkedHashSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-set/index.html), который сохраняет порядок вставки элементов. Следовательно, функции, которые зависят от порядка элементов, такие как `first()` или `last()`, возвращают предсказуемые результаты для таких множеств.

```kotlin
fun main() {
    val numbers = setOf(1, 2, 3, 4)  // по умолчанию LinkedHashSet
    val numbersBackwards = setOf(4, 3, 2, 1)

    println(numbers.first() == numbersBackwards.first()) // false
    println(numbers.first() == numbersBackwards.last()) // true
}
```

Альтернативная реализация - [`HashSet`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-set/index.html) - не сохраняет порядок элементов, поэтому при вызове функций `first()` или `last()` вернётся непредсказуемый результат. Однако `HashSet` требует меньше памяти для хранения того же количества элементов.