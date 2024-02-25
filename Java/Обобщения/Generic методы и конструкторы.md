### Дженерик методы
Одно из ключевых отличий дженерик методов — **область видимости объектов обобщённых типов**. Если в дженерик-классах объект обобщённого типа можно было объявить глобально и использовать в любом методе класса, то в дженерик-методах и конструкторах их область видимости ограничена методом или конструктором соответственно. При этом сам класс не обязательно должен быть обобщённым.

Так выглядит generic метод
```java
class ListUtils {
    public static <T> void fromArrayToList(T[] array, List<T> list) {
        for (T o : array) {
            list.add(o);
        }
    }
}
```

А так его использование
```java
PaperTrash[] paperArray = new PaperTrash[10];
ArrayList<PaperTrash> paperList = new ArrayList<>();

ListUtils.<PaperTrash>fromArrayToList(paperArray, paperList);

//или так
ListUtils.fromArrayToList(paperArray, paperList);
```

Дженерик-метод не должен быть только статическим. Если метод возвращает значение, то обобщённый тип может использоваться как возвращаемый тип данных

```java
class ListUtils {

    public <T> ArrayList<T> convertArrayToList(T[] array) { // Используем дженерик-тип в качестве возвращаемого типа данных
        ArrayList<T> list = new ArrayList<>();
        for (T o : array) {
            list.add(o);
        }
        return list;
    }
}
```

- Параметров типа может быть несколько, и им можно задавать ограничения на наследуемый класс и реализуемые интерфейсы.
- Сам класс не должен быть дженерик-классом, чтобы в нём могли быть дженерик-методы.
- Если класс обобщённый, то его имена параметров типа должны отличаться от имён параметров типа дженерик-методов этого класса.
- В классе может быть несколько дженерик-методов, и имена их параметров типа необязательно должны отличаться, поскольку они имеют разные области видимости

### Дженерик конструкторы
**Дженерик-конструкторы** позволяют использовать обобщённые типы данных внутри конструкторов независимо от того, является ли сам класс параметризованным или нет.

```java
class TrashDetails {
    private String type;
    private float weight;

    public TrashDetails(String type, float weight) {
        this.type = type;
        this.weight = weight;
    }

    public <T extends Trash & Weighable> TrashDetails(T trash) {
        type = trash.getType();
        weight = trash.getWeight();
    }
}

TrashDetails details = new TrashDetails(napkin);
```

Указание типа данных в угловых скобках необязательно, потому что компилятор определяет его по типу передаваемого в конструктор объекта.

Кроме того, дженерик-конструктор может быть конструктором по умолчанию в классе:

```java
class TrashDetails {
    private String type;
    private float weight;

    public <T extends Trash & Weighable> TrashDetails(T trash) {
        type = trash.getType();
        weight = trash.getWeight();
    }
}
```