---
tags:
  - Kotlin
  - тип_данных
cssclasses: []
---
Чтобы объявить `enum` в Kotlin, нужно использовать ключевые слова `enum class`.

Kotlin явно говорит, что `enum`— тоже класс:

```kotlin
// тема на телефоне
enum class PhoneTheme {
    LIGHT, // светлая
    DARK, // тёмная
    ADAPTABLE // адаптируемая под время суток
} 
```

### В Kotlin `enum` реализует интерфейсы:

```kotlin
interface Localizable {

    fun getLocalizedName(): String
}

// помним, что в Kotlin ключевое слово implements и extends заменены двоеточием ':'
enum class PhoneTheme : Localizable {
    LIGHT {
        override fun getLocalizedName(): String = "Светлая"
    },
    DARK {
         override fun getLocalizedName(): String = "Тёмная"
    },
    ADAPTABLE {
         override fun getLocalizedName(): String = "Адаптивная"
    }
} 
```

Такая запись тоже возможна в Kotlin:

```kotlin
enum class PhoneTheme : Localizable {
    LIGHT,
    DARK,
    ADAPTABLE;

    override fun getLocalizedName(): String =
        when (this) {
            LIGHT -> "Светлая"
            DARK -> "Тёмная"
            ADAPTABLE -> "Адаптивная"
        }
} 
```

### Перечисление в Kotlin имеет конструкторы, поля и методы:

```kotlin
// конструктор объявляется по тому же принципу, что и в обычных классах
enum class PhoneTheme(val localizedName: String) {
    LIGHT("Светлая"),
    DARK("Тёмная"),
    ADAPTABLE("Адаптивная");

    // enum может хранить поля
    val size = values().size

    // методы также могут храниться в enum
    fun getEnLocalization(): String =
        when (this) {
            LIGHT -> "Light"
            DARK -> "Dark"
            ADAPTABLE -> "Adaptable"
        }
} 
```

Перечисление легко в понимании и использовании и очень полезно, когда нам нужно описать ограниченный список элементов.

Перечисление предоставляет всё необходимое для элегантного и наглядного решения задачи.

### Все перечисления обладают двумя встроенными свойствами:
1. `name`: возвращает название константы в виде строки
2. `ordinal`: возвращает порядковый номер константы

```kotlin
enum class Day(val value: Int) {
	MONDAY(1), TUESDAY(2), WEDNESDAY(3),
	THURSDAY(4), FRIDAY(5), SATURDAY(6),
	SUNDAY(7)
}

fun main() {
	val day1: Day = Day.FRIDAY
	println(day1.name) //FRIDAY
	println(day1.ordinal) //4
}
```
Кроме того, в Kotlin нам доступны вспомогательные функции:
1. `valueOf(value: String)`: возвращает объект перечисления по названию константы
2. `values()`: возвращает массив констант текущего перечисления

```kotlin
fun main() {
	for(day in Day.values()) {
		println(day)
	}
	println(Day.valueOf("FRIDAY"))
}
```

### Анонимные классы и реализация интерфейсов

Константы перечисления могут определять анонимные классы, которые могут иметь собственные методы и свойства или реализовать абстрактные методы класса перечисления:

```kotlin
enum class DayTime {
	DAY {
		override val startHour = 6
		override val endHour = 21
		override fun printName() {
			println("День")
		}
	},
	NIGHT {
		override val startHour = 22
		override val endHour = 5
		override fun printName() {
			println("Ночь")
		}
	};

	abstract fun printName()
	abstract val startHour: Int
	abstract val endHour: Int
}

fun main() {
	DayTime.DAY.printName() //День
	DayTime.NIGHT.printName() //Ночь

	println("Day from ${DayTime.DAY.startHour} to ${DayTime.DAY.endHour}")
}
```

В данном случае класс перечисления DayTime определяет абстрактный метод `printName()` и две переменных - `startHour` (начальный час) и `endHour` (конечный час). А константы определяют анонимные классы, которые реализуют эти свойства и функцию.
### Хранение состояния

Нередко перечисления применяются для хранения состояния в программе. И в зависимости от этого состояния мы можем направить действие программы по определенному пути. Например, определим перечисление, которое представляет арифметические операции, и функцию, которая в зависимости от переданной операции выполняет то или иное действие:

```kotlin
fun main() {
	println(operate(5, 6, Operation.ADD))
	println(operate(5, 6, Operation.SUBTRACT))
	println(operate(5, 6, Operation.MULTIPLY))
}

enum class Operation {
	ADD, SUBTRACT, MULTIPLY
}
fun operate(num1: Int, num2: Int, operation: Operation): Int {
	when(operation) {
		Operation.ADD -> return num1 + num2
		Operation.SUBTRACT -> return num1 - num2
		Operation.MULTIPLY -> return num1 * num2
	}
}
```

Функция `operate()` принимает два числа - операнды операции и тип операции в виде перечисления Operation. И в зависимоси от значения перечисления возвращает либо сумму, либо разность, либо произведение двух чисел.