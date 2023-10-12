`companion object`, в переводе с английского **класс-компаньон**:

```kotlin
class Story(
    val text: String,
    val genre: String
) {

    companion object {
        const val GENRE_FANTASY = "Фэнтези"
        const val GENRE_ADVENTURE = "Приключения"
    }
} 
```

Обращение к объектам `companion object` происходит, как и обращение к статическим полям в Java:

```kotlin
val fantasyStory: Story = Story("Длинная эпическая история про драконов и магию", Story.GENRE_FANTASY)
val adventureStory: Story = Story("История про путешествия в горы за сокровищами инков", Story.GENRE_ADVENTURE)  
```

Класс-компаньон `companion object` хранит в себе методы аналогично статическим методам в Java:

```kotlin
// мы объявили конструктор с ключевым словом private
class Story private constructor(
    val text: String,
    val genre: String
) {

    companion object {
        // переменные в companion object могут иметь различные модификатора доступа
        private const val GENRE_FANTASY = "Фэнтези"
        private const val GENRE_ADVENTURE = "Приключения"

        // теперь мы можем создавать истории только с ипользованием этих жанров
        fun createFantasyStory(story: String): Story = Story(story, GENRE_FANTASY)
        fun createAdventureStory(story: String): Story = Story(story, GENRE_ADVENTURE)
    }
} 
```

Обращение к методам `companion object` происходит тем же путём, что и обращение к переменным:

```kotlin
val fantasyStory: Story = Story.createFantasyStory("Длинная эпическая история про драконов и магию")
val adventureStory: Story = Story.createAdventureStory("История про путешествия в горы за сокровищами инков") 
```

Сам `companion object` тоже имеет разные модификаторы доступа, тем самым ограничивая доступ к его членам в зависимости от модификатора доступа.

Хотя такие члены вспомогательных объектов и выглядят, как статические члены в других языках программирования, во время выполнения они являются членами реальных объектов и могут реализовывать, к примеру, интерфейсы.

```kotlin
interface Factory<T> {
    fun create(): T
}

class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}

val f: Factory<MyClass> = MyClass
```

Однако в JVM вы можете статически генерировать методы вспомогательных объектов и полей, используя аннотацию `@JvmStatic`