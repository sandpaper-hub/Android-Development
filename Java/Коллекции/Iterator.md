`Iterator` - это интерфейс в Java, который предоставляет универсальный способ обхода элементов в коллекциях. Он является частью Java Collections Framework и определен в пакете `java.util`.

Интерфейс `Iterator` содержит три основных метода:

1. **`boolean hasNext()`**: Возвращает `true`, если в коллекции есть следующий элемент, и `false`, если элементов больше нет.

2. **`E next()`**: Возвращает следующий элемент в коллекции. Если элементов больше нет, вызов этого метода может привести к исключению `NoSuchElementException`.

3. **`void remove()`**: Удаляет текущий элемент из коллекции (необязательная операция). Обычно этот метод вызывается после метода `next()`.

Пример использования `Iterator` с коллекцией `ArrayList`:

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class IteratorExample {
    public static void main(String[] args) {
        // Создаем список
        List<String> myList = new ArrayList<>();
        myList.add("One");
        myList.add("Two");
        myList.add("Three");

        // Получаем итератор
        Iterator<String> iterator = myList.iterator();

        // Используем итератор для обхода коллекции
        while (iterator.hasNext()) {
            String element = iterator.next();
            System.out.println(element);
        }
    }
}
```

В этом примере создается список `ArrayList`, заполняется несколькими элементами, а затем получается итератор с помощью метода `iterator()`. Итератор используется для обхода списка с использованием методов `hasNext()` и `next()`.

Использование итератора позволяет обходить коллекцию без знания ее внутренней структуры и обеспечивает стандартизированный способ доступа к элементам.