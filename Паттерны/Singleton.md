**Singleton — это паттерн проектирования**, который гарантирует, что у класса есть только один экземпляр, и предоставляет к нему глобальную точку доступа. Такой точкой доступа может быть метод или переменная, которая будет доступна в любом месте, где доступен Singleton.

## Java
Чтобы запретить создавать экземпляры какого-то класса, достаточно лишь скрыть конструктор по умолчанию от публичного доступа, используя ключевое слово `private`. Для создания экземпляра нам нужен публичный статический метод, который и будет контролировать созданные экземпляры Singleton:

```Java
class OriginalStoryCreator {

    // создаём приватную статическую переменную
    private static OriginalStoryCreator instance;

    private OriginalStoryCreator() {
        writeStory();
    }

    private boolean isStoryReady = false;

    private void writeStory() {
        // очень долгая операция, ведь написать новую историю не так просто
        isStoryReady = true;
    }

    public void tellStory() {
        if (isStoryReady) {
            System.out.println("Рассказать крутую историю о приключениях");
        } else {
            System.out.println("История ещё не дописана, немножечко терпения");
        }
    }

    // создаём публичный метод, который будет возвращать объект создателя историй
    public static OriginalStoryCreator getInstance() {
        // если объект ещё не создан, то его нужно создать
        if (instance == null) {
            instance = new OriginalStoryCreator();
        }

        // вернуть существующий экзмпляр создателя историй
        return instance;
    }
} 
```

Теперь единственный способ написать историю — получить экземпляр класса `OriginalStoryCreator` с помощью метода `getInstance()`:

```Java
public static void main(String[] args) {
    final OriginalStoryCreator originalStoryCreator = OriginalStoryCreator.getInstance();
    final OriginalStoryCreator anotherOriginalStoryCreator = new OriginalStoryCreator(); // здесь будет ошибка компиляции "'OriginalStoryCreator()' has private access in 'OriginalStoryCreator'"
} 
```


## Kotlin
Синглтон очень полезный паттерн программирования, и Kotlin позволяет объявлять его, используя ключевое слово [[object Kotlin|object]]:

```kotlin
object OriginalStoryCreator {

    init {
        writeStory()
    }

    private var isStoryReady: Boolean = false

    private fun writeStory() {
        // очень долгая операция, ведь написать новую историю не так просто
        isStoryReady = true
    }

    fun tellStory() {
        if (isStoryReady) {
            println("Рассказать крутую историю о приключениях")
        } else {
            println("История ещё не дописана, немножечко терпения")
        }
    }
} 
```

Это действие называется **объявление объекта**. Под капотом экземпляр такого объекта (объект как `object` и объект как экземпляр класса — разные понятия) создаётся только тогда, когда кто-то обратится к нему впервые.
Для работы с такими объектами мы используем синтаксис, который очень похож на работу со статическими методами и полями в Java.

Объект **всегда** существует только в одном экземпляре. Все свойства в нём тоже лишь в одном экземпляре, как если бы они все были `static` в Java.

Внутри объектов мы можем объявлять константы времени компиляции:

```kotlin
object OriginalStoryCreator {
    
    const val STORY_SIZE: Int = 100
    
    // тело объекта
} 
```

Ключевое слово *const* означает, что данная переменная является константой времени компиляции.

Объекты могут иметь родительские классы или реализовывать интерфейсы:

```kotlin
interface StoryKeeper

abstract class Writer

object OriginalStoryCreator : Writer(), StoryKeeper { ... } 
```