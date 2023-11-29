Kotlin основан на Java и полностью совместим с кодом на Java. И значит это следующее:

- Любой код на Kotlin может быть вызван из кода на Java и наоборот.
- Любой код, написанный на Kotlin, тем или иным способом можно написать на Java.

Анонимные классы в Kotlin решают те же задачи, что и в Java, но имеют другой синтаксис.

```kotlin
interface SomeInterface {
    fun someMethod()
    fun anotherMethod()
}

fun main() {
    // для создания анонимного экземпляра класса мы как бы создаем object, который реализует наш интерфейс
    val someInterface = object : SomeInterface {
        override fun someMethod() {}
        override fun anotherMethod() {}
        
        
    }
}
```

1. Чтобы создать [[Анонимные объекты Kotlin|объект анонимного]] класса, который наследуется от какого-то типа или типов, укажите этот тип после `object` и двоеточия `:`.
2. Затем реализуйте или переопределите члены этого класса, как если бы вы наследовали от него.

Фактически мы создаём анонимный объект, которые реализует необходимый нам интерфейс.

Если у супертипа есть конструктор, например, абстрактный класс, то в него должны быть переданы соответствующие параметры.

```kotlin
abstract class SomeAbstractClass(param: Int) {

    abstract fun someMethod()
}

fun main() {
    val someAbstractClass = object : SomeAbstractClass(param = 10) { // вызываем конструктор класса SomeAbstractClass и передаем в него именованный параметр - 10
        override fun someMethod() {}
        
        
    }
}
```

При наследовании от класса, у которого есть параметры, в конструкторе тоже нужно передать параметры, как если бы мы создавали обычный экземпляр класса.

При создании анонимного класса можно создать для него параметры внутри класса:
```kotlin
val someAbstractClass = object : SomeInterface {

    // параметры внутри анонимного класса
    val innerParam: String = "Я люблю Практикум"
    val counter: Int = 100

    override fun someMethod() {}
} 
```

В отличие от Java, в Kotlin анонимные классы могут реализовывать больше одного интерфейса. При этом важно помнить, что наследоваться можно только от одного класса.

```kotlin
abstract class SomeAbstractClass(param: Int) {
    abstract fun someMethod()
}

interface SomeInterface {
    fun someMethodFromInterface()
}

interface AnotherInterface {
    fun anotherMethod()
}

fun main() {
    // анонимный класс наследуется от абстрактного класса SomeAbstractClass и реализует интерфейсы SomeInterface и AnotherInterface
    val someAbstractClass = object : SomeAbstractClass(param = 10), SomeInterface, AnotherInterface {
            
                 // компилятор обяжет нас реализовать все абстрактные методы из всех классов и интерфейсов
        override fun someMethod() {} // метод абстрактного класса SomeAbstractClass 
        override fun someMethodFromInterface() {} // метод интерфейса SomeInterface
        override fun anotherMethod() {} // метод интерфейса AnotherInterface
        
    }
} 
```