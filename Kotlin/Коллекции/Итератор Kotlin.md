Для обхода элементов коллекции стандартная библиотека Kotlin поддерживает механизм _итераторов_ - объектов, которые предоставляют доступ к элементам последовательно, не раскрывая базовую структуру коллекции. Итераторы полезны, когда вам нужно обработать все элементы коллекции один за другим, например, вывести в лог их значения или обновить.

Итераторы доступны всем наследникам интерфейса [`Iterable<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/index.html), включая `Set` и `List`, путём вызова функции [`iterator()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterable/iterator.html).

При получении итератора он сначала указывает на первый элемент коллекции; вызов функции [`next()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-iterator/next.html) возвращает этот элемент и перемещает позицию итератора на следующий элемент, если такой существует.

Как только итератор проходит через последний элемент, его больше нельзя использовать для извлечения элементов; его также нельзя вернуть в предыдущее положение. Чтобы снова перебрать коллекцию, нужно создать новый итератор.

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val numbersIterator = numbers.iterator()
    while (numbersIterator.hasNext()) {
        println(numbersIterator.next())
    }
}

// В логе будет:
// one
// two
// three
// four
```

Другой способ перебрать `Iterable` коллекцию - это всем известный цикл `for`. При использовании `for` вы неявно получаете итератор. Поэтому, приведённый ниже код эквивалентен предыдущему примеру:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    for (item in numbers) {
        println(item)
    }
}

// В логе будет:
// one
// two
// three
// four
```

Также, есть полезная функция `forEach()`, которая позволяет автоматически перебирать коллекцию и выполнять заданный код для каждого элемента. Перепишем пример выше:

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    numbers.forEach {
        println(it)
    }
}

// В логе будет:
// one
// two
// three
// four
```

## Итераторы списков

Для списков существует специальная реализация итератора: [`ListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/index.html). Он поддерживает итерацию списков в обоих направлениях: вперёд и назад.

Обратная итерация реализуется функциями [`hasPrevious()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/has-previous.html) и [`previous()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous.html). Кроме того, `ListIterator` предоставляет информацию об индексах элементов с помощью функций [`nextIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/next-index.html) и [`previousIndex()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list-iterator/previous-index.html).

```kotlin
fun main() {
    val numbers = listOf("one", "two", "three", "four")
    val listIterator = numbers.listIterator()
    while (listIterator.hasNext()) listIterator.next()
    println("Iterating backwards:")
    while (listIterator.hasPrevious()) {
        print("Index: ${listIterator.previousIndex()}")
        println(", value: ${listIterator.previous()}")
    }
}

// В логе будет:
// Iterating backwards:
// Index: 3, value: four
// Index: 2, value: three
// Index: 1, value: two
// Index: 0, value: one
```

Наличие возможности итерации в обоих направлениях означает, что `ListIterator` может быть использован даже после того, как он достигнет последнего элемента.

## Итераторы изменяемых коллекций

Для итерации изменяемых коллекций существует [`MutableIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/index.html), который расширяет `Iterator` с помощью функции удаления элементов [`remove()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-iterator/remove.html). Поэтому, вы можете удалять элементы из коллекции во время итерации.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "two", "three", "four")
    val mutableIterator = numbers.iterator()

    mutableIterator.next()
    mutableIterator.remove()    
    println("After removal: $numbers") // [two, three, four]
}
```

Помимо удаления элементов, [`MutableListIterator`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list-iterator/index.html) позволяет добавлять и заменять элементы во время итерации.

```kotlin
fun main() {
    val numbers = mutableListOf("one", "four", "four")
    val mutableListIterator = numbers.listIterator()

    mutableListIterator.next()
    mutableListIterator.add("two")
    mutableListIterator.next()
    mutableListIterator.set("three")   
    println(numbers) // [one, two, three, four]
}
```