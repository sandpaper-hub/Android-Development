---
tags:
  - vararg
  - spread
---
Kotlin поддерживает объявление функции с переменным числом аргументов. Достаточно поставить перед названием параметра модификатор `vararg`:

```kotlin
fun printElements(separator: String, vararg strings: String)
```

Для аргументов переменной длины в Kotlin действуют следующие правила:
- Чтобы объявить аргумент переменной длины, нужно перед указанием типа добавить `vararg` 
- Аргумент переменной длины может быть объявлен только в качестве аргумента метода или конструктора.
- Внутри метода аргумент переменной длины — всегда массив. Обратите внимание на функцию выше, мы используем переменную `strings` внутри цикла `foreach`.
- Хотя аргумент переменной длины и массив, нам не нужно передавать массив в метод.
- Такой аргумент может быть только один в методе.

```kotlin
const val SEPARATOR = "---"

fun main() {
    val months = arrayOf("Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь")
    printElements(SEPARATOR, *months)
    printElements(SEPARATOR, "Понедельник", "Вторник", "Среда", "Четверг", "Пятница", "Суббота", "Воскресенье")
    printElements(SEPARATOR, "Один", "Два", "Три")
    printElements(SEPARATOR, "Один элемент")
}

fun printElements(separator: String, vararg strings: String) {
    for (string in strings) {
        println(string)
    }
    println(separator)
}
```

Оператор `spread` `*` распаковывает массив в список значений из этого массива. Он нужен при передаче массива в качестве параметра `vararg`.

Компилятор Kotlin берёт массив и переписывает его как набор элементов за нас — будто мы сами взяли и передали в аргументы все элементы массива вручную.

Более того, такой оператор позволяет объединять несколько массивов в аргументах:

```kotlin
fun main() {
    val firstArray: Array<String> = arrayOf("Один", "Два", "Три")
    val secondArray: Array<String> = arrayOf("Пять", "Шесть")
    // компилятор распакует все элементы массивов и функцию printElements:
    // printElements("Один", "Два", "Три", "Четыре", "Пять", "Шесть") 
    printElements(*firstArray, "Четыре", *secondArray)
}

fun printElements(vararg strings: String) {
    for (string in strings) {
        println(string)
    }
} 
```