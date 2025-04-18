### SortedSet

Интерфейс SortedSet предназначен для создания коллекций, который хранят элементы в отсортированном виде (сортировка по возрастанию). SortedSet расширяет интерфейс Set, поэтому такая коллекция опять же хранит только уникальные значения. SortedSet предоставляет следующие методы:

- **`E first()`**: возвращает первый элемент набора
- **`E last()`**: возвращает последний элемент набора
- **`SortedSet<E> headSet(E end)`**: возвращает объект SortedSet, который содержит все элементы первичного набора до элемента end
- **`SortedSet<E> subSet(E start, E end)`**: возвращает объект SortedSet, который содержит все элементы первичного набора между элементами start и end
- **`SortedSet<E> tailSet(E start)`**: возвращает объект SortedSet, который содержит все элементы первичного набора, начиная с элемента start

### NavigableSet

Интерфейс NavigableSet расширяет интерфейс SortedSet и позволяет извлекать элементы на основании их значений. NavigableSet определяет следующие методы:

- **`E ceiling(E obj)`**: ищет в наборе наименьший элемент e, который больше obj (e >=obj). Если такой элемент найден, то он возвращается в качестве результата. Иначе возвращается null.
- **`E floor(E obj)`**: ищет в наборе наибольший элемент e, который меньше элемента obj (e <=obj). Если такой элемент найден, то он возвращается в качестве результата. Иначе возвращается null.
- **`E higher(E obj)`**: ищет в наборе наименьший элемент e, который больше элемента obj (e >obj). Если такой элемент найден, то он возвращается в качестве результата. Иначе возвращается null.
- **`E lower(E obj)`**: ищет в наборе наибольший элемент e, который меньше элемента obj (e <obj). Если такой элемент найден, то он возвращается в качестве результата. Иначе возвращается null.
- **`E pollFirst()`**: возвращает первый элемент и удаляет его из набора
- **`E pollLast()`**: возвращает последний элемент и удаляет его из набора
- **`NavigableSet<E> descendingSet()`**: возвращает объект NavigableSet, который содержит все элементы первичного набора NavigableSet в обратном порядке
- **`NavigableSet<E> headSet(E upperBound, boolean incl)`**: возвращает объект NavigableSet, который содержит все элементы первичного набора NavigableSet до upperBound. Параметр incl при значении true, позволяет включить в выходной набор элемент upperBound
- **`NavigableSet<E> tailSet(E lowerBound, boolean incl)`**: возвращает объект NavigableSet, который содержит все элементы первичного набора NavigableSet, начиная с lowerBound. Параметр incl при значении true, позволяет включить в выходной набор элемент lowerBound
- **`NavigableSet<E> subSet(E lowerBound, boolean lowerIncl, E upperBound, boolean highIncl)`**: возвращает объект NavigableSet, который содержит все элементы первичного набора NavigableSet от lowerBound до upperBound.
### TreeSet

Обобщенный класс `TreeSet<E>` представляет структуру данных в виде дерева, в котором все объекты хранятся в отсортированном виде по возрастанию. TreeSet является наследником класса `AbstractSet` и реализует интерфейс `NavigableSet`, а следовательно, и интерфейс `SortedSet`.

В классе TreeSet определены следующие конструкторы:

- **`TreeSet()`**: создает пустое дерево
- **`TreeSet(Collection<? extends E> col)`**: создает дерево, в которое добавляет все элементы коллекции col
- **`TreeSet(SortedSet <E> set)`**: создает дерево, в которое добавляет все элементы сортированного набора set
- **`TreeSet(Comparator<? super E> comparator)`**: создает пустое дерево, где все добавляемые элементы впоследствии будут отсортированы компаратором.

TreeSet поддерживает все стандартные методы для вставки и удаления элементов

```java
import java.util.TreeSet;

public class TreeSetExample {
    public static void main(String[] args) {
        // Создаем TreeSet для хранения строк
        TreeSet<String> treeSet = new TreeSet<>();

        // Добавляем элементы
        treeSet.add("Apple");
        treeSet.add("Banana");
        treeSet.add("Orange");
        treeSet.add("Grapes");

        System.out.println("Содержимое TreeSet: " + treeSet);

        // Попробуем добавить дубликат
        boolean isDuplicateAdded = treeSet.add("Apple");
        System.out.println("Добавлен дубликат 'Apple': " + isDuplicateAdded);

        // Удаление элемента
        treeSet.remove("Banana");

        System.out.println("Содержимое TreeSet после удаления элемента: " + treeSet);

        // Итерация по элементам в отсортированном порядке
        System.out.println("Итерация по элементам TreeSet:");
        for (String fruit : treeSet) {
            System.out.println(fruit);
        }

        // Получение первого и последнего элементов
        String firstElement = treeSet.first();
        String lastElement = treeSet.last();
        System.out.println("Первый элемент: " + firstElement);
        System.out.println("Последний элемент: " + lastElement);
    }
}
```