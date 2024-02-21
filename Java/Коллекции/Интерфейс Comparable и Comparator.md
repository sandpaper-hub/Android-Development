При добавлении новых элементов объект TreeSet автоматически проводит сортировку, помещая новый объект на правильное для него место. Однако с примитивными типами и строками всё просто, а вот с объектами других типов не однозначно.
Объект TreeSet нельзя типизировать объектами других классов, так как при добавлении объектов, TreeSet не будет знать как их сравнивать.

### Comparable
Для того, чтобы объект можно было сравнивать и сортировать, класс должен реализовывать интерфейс `Comparable<E>`. При применении он типизируется текущим классом.

```java
public class MyClass implements Comparable<MyClass> {
    private int value;

    // Конструктор, геттеры, сеттеры и другие методы

    @Override
    public int compareTo(MyClass other) {
        return Integer.compare(this.value, other.value);
    }
}
```

Интерфейс Comparable содержит один единственный метод `int compareTo(E item)`, который сравнивает текущий объект с объектом, переданным в качестве параметра. Если этот метод возвращает отрицательное число, то текущий объект будет располагаться перед тем, который передается через параметр. Если метод вернет положительное число, то, наоборот, после второго объекта. Если метод возвратит ноль, значит, оба объекта равны.

### Comparator
Интерфейс `Comparator` является альтернативным подходом для сравнения объектов. В сравнении с интерфейсом `Comparable`, `Comparator` позволяет определить порядок сравнение внешним образом. Это может быть полезно, если нет возможности изменить исходный класс или нужна возможность сравнивать объекты разными способами.

```java
import java.util.Comparator;
import java.util.TreeSet;

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}

public class TreeSetWithComparatorExample {
    public static void main(String[] args) {
        // Создаем Comparator для сравнения Person по возрасту
        Comparator<Person> ageComparator = Comparator.comparingInt(Person::getAge);

        // Используем TreeSet с переданным Comparator'ом
        TreeSet<Person> personSet = new TreeSet<>(ageComparator);

        // Добавляем объекты Person в TreeSet
        personSet.add(new Person("Alice", 25));
        personSet.add(new Person("Bob", 30));
        personSet.add(new Person("Charlie", 22));

        // Выводим отсортированный TreeSet
        System.out.println("Отсортированный TreeSet по возрасту:");
        for (Person person : personSet) {
            System.out.println(person.getName() + " - " + person.getAge() + " years old");
        }
    }
}```

##### Другие методы
1. **`thenComparing`**:
	- Создает компаратор, который сравнивает объекты по другому ключу, если текущий компаратор считает их равными.
    
```java
Comparator<Person> nameComparator = Comparator.comparing(Person::getName);
Comparator<Person> ageComparator = Comparator.comparingInt(Person::getAge); //Сначала сравниваем по имени, затем по возрасту 
Comparator<Person> combinedComparator = nameComparator.thenComparing(ageComparator);
```

2. **`reversed`**:    
    - Создает компаратор, который обращает порядок сравнения объектов.
```java
Comparator<Person> reversedAgeComparator = ageComparator.reversed();
```

3. **`nullsFirst` и `nullsLast`**:    
    - Создают компаратор, который разрешает сравнение с `null`. `nullsFirst` помещает `null` в начало, а `nullsLast` - в конец.
    
```java
Comparator<Person> ageComparatorWithNullsFirst = Comparator.nullsFirst(ageComparator);
```

### Сортировка по нескольким критериям
Начиная с JDK 8 в механизм работы компараторов были внесены некоторые дополнения. В частности, теперь можно применять сразу несколько компараторов по принципу приоритета.

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}

public class MultiCriteriaSortingExample {
    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 25));
        people.add(new Person("Bob", 30));
        people.add(new Person("Charlie", 22));
        people.add(new Person("Bob", 28));

        // Создаем компаратор для сортировки по имени, а затем по возрасту
        Comparator<Person> nameThenAgeComparator = Comparator
                .comparing(Person::getName)
                .thenComparingInt(Person::getAge);

        // Сортируем список объектов Person по нескольким критериям
        people.sort(nameThenAgeComparator);

        // Выводим отсортированный список
        for (Person person : people) {
            System.out.println(person.getName() + " - " + person.getAge() + " years old");
        }
    }
}
```

В этом примере `Comparator.comparing` используется для создания компаратора, который сравнивает объекты `Person` сначала по имени. Затем метод `thenComparingInt` используется для добавления дополнительного критерия сравнения по возрасту в случае, если имена равны.

Этот код выводит список людей, отсортированный сначала по имени в алфавитном порядке, а затем по возрасту в порядке возрастания. Вы можете использовать такой подход для добавления дополнительных критериев сортировки в порядке их приоритета.