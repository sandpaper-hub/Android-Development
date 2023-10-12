Kotlin позволяет создавать **анонимные объекты**. Объекты анонимных классов, то есть классов, которые явно не объявлены с помощью `class`, полезны для одноразового использования. Мы можем объявить их с нуля, наследовать от существующих классов или реализовать интерфейсы.

Экземпляры анонимных классов могут называться анонимными объектами, потому что они объявляются выражением, а не именем.

Чтобы создать анонимный объект, мы используем ключевое слово `object`

```kotlin
fun main() {
    val writersApprentice = object : Writer() {
        fun tellStory() {
            println("Давным давно...")
        }
    }

    writersApprentice.tellStory()
} 
```

Анонимные объекты тоже могут иметь супертипы (классы-родители, интерфейсы):

```kotlin
abstract class Writer

interface StoryKeeper

fun main() {
    // анонимный объект наследуется от класса Writer и реализует интерфейс StoryKeeper
    val writersApprentice = object : Writer(), StoryKeeper {
        fun tellStory() {
            println("Давным давно...")
        }
    }

    writersApprentice.tellStory()
} 
```

### Использование анонимных объектов в качестве возвращаемых типов значений

Когда анонимный объект используется в качестве типа local или [private](https://kotlinlang.ru/docs/visibility-modifiers.html#packages), но не [встроенного](https://kotlinlang.ru/docs/inline-functions.html) объявления (функции или свойства), все его члены доступны через эту функцию или свойство.

```kotlin
class C {
    private fun getObject() = object {
        val x: String = "x"
    }

    fun printX() {
        println(getObject().x)
    }
}
```

Если эта функция или свойство маркированы как public или встроенный private, то их тип:

- `Any`, если анонимный объект не имеет объявленного супертипа;
- Объявленный супертип анонимного объекта, если существует ровно один такой тип;
- Явно объявленный тип, если существует более одного объявленного супертипа.

Во всех этих случаях члены, добавленные в анонимный объект, недоступны. Переопределенные члены доступны, если они объявлены в фактическом типе функции или свойства.

```kotlin
interface A {
    fun funFromA() {}
}
interface B

class C {
    // Возвращаемый тип Any. x недоступен
    fun getObject() = object {
        val x: String = "x"
    }

    // Возвращаемый тип A; x недоступен
    fun getObjectA() = object: A {
        override fun funFromA() {}
        val x: String = "x"
    }

    // Возвращаемый тип B; funFromA() и x недоступны
    fun getObjectB(): B = object: A, B { // требуется явный тип возвращаемого значения
        override fun funFromA() {}
        val x: String = "x"
    }
}
```