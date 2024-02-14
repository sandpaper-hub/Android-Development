## Коллекция с элементами

Самый распространённый способ создать коллекцию - использовать функции [`listOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html), [`setOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/set-of.html), [`mutableListOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-list-of.html), [`mutableSetOf<T>()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-set-of.html). Этим функциям в качестве аргументов можно передать элементы, разделяя их запятой. В этом случае тип коллекции указывать не обязательно - компилятор сам его определит. Если же вы хотите создать пустую коллекцию, то её тип необходимо указывать явно.

```kotlin
val numbersSet = setOf("one", "two", "three", "four")
val emptySet = mutableSetOf<String>()
```

Таким же образом создаются и ассоциативные списки - при помощи функций [`mapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-of.html) и [`mutableMapOf()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/mutable-map-of.html). Ключи и значения ассоциативного списка передаются как объекты `Pair` (обычно создаются с помощью инфиксной функции `to`).

```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)
```

Обратите внимание, что нотация `to` создаёт недолговечный объект `Pair`, поэтому рекомендуется использовать его только в том случае, если производительность не критична. Чтобы избежать чрезмерного использования памяти, используйте альтернативные способы. Например, вы можете создать `MutableMap` и заполнить его с помощью операций записи. Функция [`apply()`](https://kotlinlang.ru/docs/scope-functions.html#apply) поможет создать плавную инициализацию.

```kotlin
val numbersMap = mutableMapOf<String, String>()
        .apply {
            this["one"] = "1";
            this["two"] = "2"
        }
```

## Пустая коллекция

Существуют функции для создания пустых коллекций: [`emptyList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-list.html), [`emptySet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-set.html) и [`emptyMap()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/empty-map.html). При создании пустой коллекции вы должны явно указывать тип элементов, которые будет содержать коллекция.

```kotlin
val empty = emptyList<String>()
```

## Функция-инициализатор для списков

У списков есть конструктор, принимающий размер списка и функцию-инициализатор, которая определяет значение элементов на основе их индексов.

```kotlin
fun main() {
    val doubled = List(3, { it * 2 })  // или MutableList, если вы хотите изменять содержимое
    println(doubled) // [0, 2, 4]
}
```

## Конструкторы конкретных типов

Чтобы создать коллекцию конкретного типа, например, `ArrayList` или `LinkedList`, вы можете использовать их конструкторы. Аналогичные конструкторы доступны и для реализаций `Set` и `Map`.

```kotlin
val linkedList = LinkedList<String>(listOf("one", "two", "three"))
val presizedSet = HashSet<Int>(32)
```

## Копирование коллекции

Если вам требуется создать новую коллекцию, но с элементами из существующей коллекции, то вы можете её скопировать. Операции копирования из стандартной библиотеки создают _неполные_ копии коллекций - со ссылками на исходные элементы. Поэтому изменение, внесённое в элемент коллекции, будет применено ко всем его копиям.

Функции копирования коллекций, такие как [`toList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-list.html), [`toMutableList()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-mutable-list.html), [`toSet()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/to-set.html) и другие, фиксируют состояние коллекции в определённый момент времени. Результат их выполнения - новая коллекция, но с элементами из исходной коллекции. Если вы добавите или удалите элементы из исходной коллекции, это не повлияет на копии. Копии также могут быть изменены независимо от источника.

```kotlin
fun main() {
    val sourceList = mutableListOf(1, 2, 3)
    val copyList = sourceList.toMutableList()
    val readOnlyCopyList = sourceList.toList()
    sourceList.add(4)
    println("Copy size: ${copyList.size}") // 3

    //readOnlyCopyList.add(4) // ошибка компиляции
    println("Read-only copy size: ${readOnlyCopyList.size}") // 3
}
```

Эти функции также можно использовать для преобразования типа коллекции, например, для создания множества из списка или наоборот.

```kotlin
fun main() {
    val sourceList = mutableListOf(1, 2, 3)    
    val copySet = sourceList.toMutableSet()
    copySet.add(3)
    copySet.add(4)    
    println(copySet) // [1, 2, 3, 4]
}
```

В качестве альтернативы вы можете создать новую ссылку на тот же экземпляр коллекции. Новую ссылку можно создать, инициализировав новую переменную для коллекции и записав в нее существующую коллекцию. Однако, изменив новую коллекцию, изменения отразятся во всех ссылках на эту коллекцию.

```kotlin

fun main() {
    val sourceList = mutableListOf(1, 2, 3)
    val referenceList = sourceList
    referenceList.add(4)
    println("Source size: ${sourceList.size}") // 4
}
```

Подобная инициализация может использоваться для того, чтобы ограничить доступ на изменение коллекции. Например, вы создаёте ссылку `List` на основе `MutableList`, если вы попытаетесь изменить коллекцию с помощью этой ссылки, то компилятор выдаст ошибку.

```kotlin
fun main() {
    val sourceList = mutableListOf(1, 2, 3)
    val referenceList: List<Int> = sourceList
    //referenceList.add(4) // ошибка компиляции
    sourceList.add(4)
    println(referenceList) // показывает текущее состояние sourceList - [1, 2, 3, 4]
}
```