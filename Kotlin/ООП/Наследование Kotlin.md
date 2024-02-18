---
tags:
  - наследование
  - Kotlin
  - open
  - abstract
  - override
  - super
---
В Kotlin нет ключевых слов `extends` и `implements`, как это было в Java. В Kotlin при наследовании и реализации интерфейса используется знак двоеточия `:`.

```kotlin
// наследование от абстрактного класса. 
abstract class Parent
class Child : Parent()

// реализация интерфейса
interface Interface
class InterfaceImplementation : Interface 
```

Ключевое слово `abstract` в Kotlin работает как в Java.

Если у родительского класса есть конструктор с параметрами, то мы должны вызвать его при определении класса-наследника:

```kotlin
abstract class Parent(val parentParam: String)
interface InterfaceOne
interface InterfaceTwo

// параметр в родительский класс может быть передан из одного из параметров класса-наследника
class Child(
    paramForParent: String,
    val paramOne: Int,
    val paramTwo: String
) : Parent(parentParam = paramForParent), InterfaceOne, InterfaceTwo

// а может иметь константу
class AnotherChild : Parent(parentParam = "param"), InterfaceOne, InterfaceTwo 
```

### Ключевое слово `open`

В книге «Java — эффективное программирование» Джошуа Блох написал:

> Делайте каждый класс или член как можно более недоступным.

Ведь чем меньше шансов класс изменить, тем меньше шансов допустить ошибку. В Kotlin решили воплотить это правило на уровне синтаксиса и сделали все классы, кроме абстрактных, по умолчанию неизменяемыми, то есть `final`.

В отличие от Java, в Kotlin мы должны явно указать то, что мы хотим наследоваться от данного класса. Для этого в Kotlin используется ключевое слово `open`:

```kotlin
// ключевое слово open явно указывает на то, что мы можем наследоваться от этого класса
open class OpenClass
class ChildOfOpenClass : OpenClass() // такое наследование корректно

// класс FinalClass не имеет ключевого слова open. Наследоваться от него нельзя
class FinalClass
class ChildOfFinalClass : FinalClass() // компилятор выдаст ошибку 'This type is final, so it cannot be inherited from'
```

^0ae296

Kotlin провоцирует нас всё запрещать и явно говорить компилятору:

> Здесь я хочу ослабить неизменяемость объекта. Я понимаю, что так мой код станет более уязвим.

Такой подход уменьшает вероятность того, что какой-то объект будет являться изменяемым не потому, что так было запланировано при разработке, а потому, что кто-то просто забыл дописать какое-то ключевое слово вроде `final`.

### Ключевое слово `override`

В Kotlin есть специальное ключевое слово для переопределения методов, которое обязательно

```kotlin
abstract class AbstractClass {

    abstract fun doWork()
}

class Child : AbstractClass() {

    // мы должны явно указать, что этот метод переопределён от родительского класса
    override fun doWork() {
        // do something
    }
}

class AnotherChild : AbstractClass() {

    // здесь компилятор выдаст нам ошибку ''doWork' hides member of supertype 'AbstractClass' and needs 'override' modifier'
    fun doWork() {
        // do something
    }
}
```

Ключевое слово `override` нужно указывать при реализации методов интерфейса:

```kotlin
interface Interface {

    fun doWork()
}

class Child : Interface {

    // при реализации интерфейса мы должны указывать ключевое слово `override`
    override fun doWork() {
        // do something
    }
} 
```

Переопределять можно не только абстрактные методы, определённые в интерфейсах или абстрактных классах, но и методы, имеющие реализацию, если класс и сам метод отмечен ключевым словом `open`:

```kotlin
open class OpenClass {

    // функция отмечена ключевым словом open, а значит, она может быть переопределена в классах-наследниках
    open fun openFun() {
        println("Нужно немного веселья для родителей.")
    }
}

class ChildOfOpenClass : OpenClass() {

    // мы должны указать ключевое слово override, если хотим переопределить функцию openFun()
    override fun openFun() {
        println("Нужно немного веселья для детей.")
    }
}

fun main() {
    val openClass = OpenClass()
    val childOfOpenClass = ChildOfOpenClass()
    
    openClass.openFun()
    childOfOpenClass.openFun()
}
```

Но если класс не будет `open` или `abstract`, то от него нельзя будет наследоваться, а значит, ключевое слово `open` у функции не будет иметь никакого эффекта. IDE подсветит вам предупреждение *'open' has no effect in a final class'*.

### Переопределение свойств класса

Ещё одно отличие Kotlin от Java — возможность переопределения свойств.

```kotlin
interface Interface {

    // мы можем указывать абстрактное свойство внутри интерфейса
    val property: Int
}

// переопеределить свойство можно в конструкторе класса, реализующего интерфейс
class Child(override val property: Int) : Interface

// можно переопределить как свойство в теле класса
class AnotherChild : Interface {
    override val property: Int = 10
}

// можно переопределить get-метод свойства
class YetAnotherChild : Interface {
    override val property: Int
        get() = Random.nextInt()
} 
```

В  абстрактных классах мы можем создавать абстрактные свойства:

```kotlin
abstract class AbstractClass {

    // для абстрактных классов абстрактные свойства должны быть отмечены ключевым словом abstract
    abstract val property: Int

    // свойства, как и методы, могут иметь модификатор open. В таком случае их переопределение необязательно
    open val openProperty: Int = 10
}

class Child(
    override val property: Int, // переопределяем абстрактное свойство
    override val openProperty: Int // переопределяем open свойство (необязательно)
) : AbstractClass() 
```

### Ключевое слово `super`

В Kotlin тоже есть ключевое слово `super`, которое позволяет вызывать функции и свойства своего суперкласса:

Скопировать кодKOTLIN

```kotlin
open class OpenClass {

    protected val sizeOfFun: Int = 10

    open fun openFun() {
        println("Нужно немного веселья для родителей. Размер веселья = $sizeOfFun")
    }
}

class ChildOfOpenClass : OpenClass() {

    // мы можем обратиться к переменной родительского класса, используя ключевое слово super
    private val sizeOfChildFun: Int = super.sizeOfFun * 2

    override fun openFun() {
        super.openFun() // вызываем родительскую функцию openFun() с помощью ключевого слова super
        println("Нужно немного веселья для детей. Размер веселья = $sizeOfChildFun")
    }
}

val childOfOpenClass = ChildOfOpenClass()
childOfOpenClass.openFun() 
```