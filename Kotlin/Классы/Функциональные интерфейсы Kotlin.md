Функциональными интерфейсами называются интерфейсы, у которых есть только один абстрактный метод.
Для объявления функционального интерфейса в Kotlin есть специальное ключевое слово `fun` как функция:

```kotlin
fun interface FunInterface {
    fun doSomething()
} 
```

Ключевое слово `fun` при объявлении интерфейса даёт не только явное указание, что этот интерфейс функциональный, оно позволяет использовать интерфейс в качестве лямбда-функции.

```kotlin
fun interface FunInterface {
    fun doSomething()
}

interface NoneFunInterface {
    fun doSomething()
}

fun main() {
    val functionalInterface = FunInterface { /* ваш код здесь */ } // мы можем использовать функциональный интерфейс как лямбду 
    val noneFunctionalInterface = object : NoneFunInterface { // здесь компилятор не позволит нам использовать лямбду и нам придется явно описать реализацию интерфейса
        override fun doSomething() {
            // ваш код здесь
            
        }
    }
}
```

Если добавить в интерфейс с ключевым словом `fun` ещё один метод, то мы получим ошибку компиляции.

```kotlin
// ошибка 'Fun interfaces must have exactly one abstract method'
fun interface FunInterface {
    fun doSomething()
    fun anotherMethod()
} 
```

Пример использования функциональных интерфейсов с несколькими параметрами:

```kotlin
fun interface FunInterfaceWithParam {
    fun doSomething(param: String)
}

fun interface FunInterfaceWithDoubleParam {
    fun doSomething(first: Int, second: String)
}

fun main() {
    // как и в Java, внутри лямбда выражения мы можем указывать параметры и использовать их
    val funInterfaceWithParam = FunInterfaceWithParam { param -> println(param) }
        
    // параметров может быть более одного и количество выражений внутри лямбды так же может неограниченное количество
    val funInterfaceWithDoubleParam = FunInterfaceWithDoubleParam { first, second ->
        // каждый новый вызов метода внутри лямбды должен идти с новой строки
        println(first)
        println(second)
        
    }
}
```

### `it` — неявное имя единственного параметра

Часто в лямбда-выражении есть только один параметр.

Если компилятор способен самостоятельно определить сигнатуру, то объявление параметра можно опустить вместе с `->`. Параметр будет неявно объявлен под именем `it`.

```kotlin
// поскольку метод функционального интерфейса имеет только один параметр, можем использовать it
// в данной ситуации it будет иметь тип String
val funInterfaceWithParam = FunInterfaceWithParam { println(it) } 
```