Модификатор **static** в Java напрямую связан с классом. Если поле статично, значит оно принадлежит классу, если метод статичный — аналогично: он принадлежит классу. Исходя из этого, можно обращаться к статическому методу или полю, используя имя класса. Например, если поле count статично в классе `Counter`, значит, вы можете обратиться к переменной запросом вида: `Counter.count`.

**Static** — модификатор, применяемый к `полю`, `блоку`, `методу` или `внутреннему классу`. Данный модификатор указывает на привязку субъекта к текущему классу.

### 1. **Статические поля (static fields):**
   - Статические поля принадлежат классу, а не экземпляру класса. Они используются, например, для хранения общих данных для всех экземпляров класса.
   - Объявляются с использованием ключевого слова `static`.
   - Общий доступ ко всем экземплярам класса.

   ```java
   public class Пример {
       static int статическоеПоле;

       public static void main(String[] args) {
           Пример.статическоеПоле = 42;
       }
   }
   ```

### 2. **Статические методы (static methods):**
   - Статические методы также принадлежат классу и вызываются через имя класса, а не через объект класса.
   - Могут использоваться для выполнения операций, не зависящих от конкретного экземпляра класса.
   - Не имеют доступа к нестатическим (обычным) полям и методам класса.

   ```java
   public class Пример {
       static void статическийМетод() {
           System.out.println("Это статический метод");
       }

       public static void main(String[] args) {
           Пример.статическийМетод();
       }
   }
   ```

### 3. **Статические блоки (static blocks):**
   - Статические блоки представляют собой блоки кода, которые выполняются при загрузке класса.
   - Используются для инициализации статических полей или выполнения других статических операций.

   ```java
   public class Пример {
       static {
           System.out.println("Этот статический блок выполняется при загрузке класса");
       }
   }
   ```

### 4. **Статические вложенные классы (static nested classes):**
   - Статические вложенные классы привязаны к внешнему классу, но создаются без привязки к экземпляру внешнего класса.
   - Они могут использоваться для создания вспомогательных классов, не требующих доступа к экземплярам внешнего класса.

   ```java
   public class ВнешнийКласс {
       static class СтатическийВложенныйКласс {
           // код
       }
   }
   ```

### Примечание:
1. Нельзя получить доступ к НЕ статическим членам класса, внутри статического контекста, как вариант, метода или блока. Результатом компиляции приведенного ниже кода будет ошибка:
    
```java
    public class Counter{ 
    private int count; 
    public static void main(String args[]){
        System.out.println(count); //compile time error 
	    }
    }
```
    
    Это одна из наиболее распространённых ошибок допускаемых программистами Java, особенно новичками. Так как метод `main` статичный, а переменная `count` нет, в этом случае метод `println`, внутри метода `main` выбросит “Compile time error”.
    
2. В отличие от локальных переменных, статические поля и методы НЕ потокобезопасны (Thread-safe) в Java. На практике это одна из наиболее частых причин возникновения проблем связанных с безопасностью мультипоточного программирования. Учитывая что каждый экземпляр класса имеет одну и ту же копию статической переменной, то такая переменная нуждается в защите — «залочивании» классом. Поэтому при использовании статических переменных, убедитесь, что они должным образом синхронизированы (synchronized), во избежание проблем, например таких как «состояние гонки» (race condition).
    
3. Статические методы имеют преимущество в применении, т.к. отсутствует необходимость каждый раз создавать новый объект для доступа к таким методам. Статический метод можно вызвать, используя тип класса, в котором эти методы описаны. Именно поэтому, подобные методы как нельзя лучше подходят в качестве методов-фабрик (`factory`), и методов-утилит (`utility`). Класс `java.lang.Math` — замечательный пример, в котором почти все методы статичны, по этой же причине классы-утилиты в Java финализированы (`final`).
    
4. Другим важным моментом является то, что вы НЕ можете переопределять (`Override`) статические методы. Если вы объявите такой же метод в классе-наследнике (`subclass`), т.е. метод с таким же именем и сигнатурой, вы лишь «спрячете» метод суперкласса (`superclass`) вместо переопределения. Это явление известно как сокрытие методов (`hiding methods`). Это означает, что при обращении к статическому методу, который объявлен как в родительском, так и в дочернем классе, во время компиляции всегда будет вызван метод исходя из типа переменной. В отличие от переопределения, такие методы не будут выполнены во время работы программы. Рассмотрим пример:
    
```java
class Vehicle{
    public static void  kmToMiles(int km){
		System.out.println("Внутри родительского класса/статического метода");
	}
 }  

class Car extends Vehicle{
    public static void  kmToMiles(int km){
        System.out.println("Внутри дочернего класса/статического метода ");      
    }
 }  

public class Demo{
	public static void main(String args[]){
	    Vehicle v = new Car();
        v.kmToMiles(10);
	}
}
```
    
    Вывод в консоль:
    
    _Внутри родительского класса/статического метода_
    
    Код наглядно демонстрирует: несмотря на то, что объект имеет тип `Car`, вызван статический метод из класса `Vehicle`, т.к. произошло обращение к методу во время компиляции. И заметьте, ошибки во время компиляции не возникло!
    
5. Объявить статическим также можно и класс, за исключением классов верхнего уровня. Такие классы известны как «вложенные статические классы» (`nested static class`). Они бывают полезными для представления улучшенных связей. Яркий пример вложенного статического класса — `HashMap.Entry`, который предоставляет структуру данных внутри `HashMap`. Стоит заметить, также как и любой другой внутренний класс, вложенные классы находятся в отдельном файле .class. Таким образом, если вы объявили пять вложенных классов в вашем главном классе, у вас будет 6 файлов с расширением .class. Ещё одним примером использования является объявление собственного компаратора (`Comparator`), например компаратор по возрасту (`AgeComparator`) в классе сотрудники (`Employee`).
    
6. Модификатор static также может быть объявлен в статичном блоке, более известным как «Статический блок инициализации» (`Static initializer block`), который будет выполнен во время загрузки класса. Если вы не объявите такой блок, то Java соберёт все статические поля в один список и выполнит его во время загрузки класса. Однако, статичный блок НЕ может пробросить перехваченные исключения, но может выбросить не перехваченные. В таком случае возникнет «Exception Initializer Error». На практике, любое исключение возникшее во время выполнения и инициализации статических полей, будет завёрнуто Java в эту ошибку. Это также самая частая причина ошибки «No Class Def Found Error», т.к. класс не находился в памяти во время обращения к нему.
    
7. Полезно знать, что статические методы связываются во время компиляции, в отличие от связывания виртуальных или не статических методов, которые связываются во время исполнения на реальном объекте. Следовательно, статические методы не могут быть переопределены в Java, т.к. полиморфизм во время выполнения не распространяется на них. Это важное ограничение, которое необходимо учитывать, объявляя метод статическим. В этом есть смысл, только тогда, когда нет возможности или необходимости переопределения такого метода классами-наследниками. Методы-фабрики и методы-утилиты хорошие образцы применения модификатора static. Джошуа Блох выделил несколько преимуществ использования статичного метода-фабрики перед конструктором, в книге «Effective Java», которая является обязательной для прочтения каждым программистом данного языка.
    
8. Важным свойством статического блока является инициализация. Статические поля или переменные инициализируются после загрузки класса в память. Порядок инициализации сверху вниз, в том же порядке, в каком они описаны в исходном файле Java класса. Поскольку статические поля инициализируются на потокобезопасный манер, это свойство также используется для реализации паттерна `Singleton`. Если вы не используется список `Enum` как `Singleton`, по тем или иным причинам, то для вас есть хорошая альтернатива. Но в таком случае необходимо учесть, что это не «ленивая» инициализация. Это означает, что статическое поле будет проинициализировано ещё ДО того как кто-нибудь об этом «попросит». Если объект ресурсоёмкий или редко используется, то инициализация его в статическом блоке сыграет не в вашу пользу.
    
9. Во время сериализации, также как и `transient` переменные, статические поля не сериализуются. Действительно, если сохранить любые данные в статическом поле, то после десериализации новый объект будет содержать его первичное (по-умолчанию) значение, например, если статическим полем была переменная типа `int`, то её значение после десериализации будет равно нулю, если типа `float` – 0.0, если типа `Object` – `null`. Честно говоря, это один из наиболее часто задаваемых вопросов касательно сериализации на собеседованиях по Java. Не храните наиболее важные данные об объекте в статическом поле!
    
10. И напоследок, поговорим о `static import`. Данный модификатор имеет много общего со стандартным оператором `import`, но в отличие от него позволяет импортировать один или все статические члены класса. При импортировании статических методов, к ним можно обращаться как будто они определены в этом же классе, аналогично при импортировании полей, мы можем получить доступ без указания имени класса. Данная возможность появилась в Java версии 1.5, и при должном использовании улучшает читабельность кода. Наиболее часто данная конструкция встречается в тестах _JUnit_, т.к. почти все разработчики тестов используют `static import` для assert методов, например `assertEquals()` и для их перегруженных дубликатов.
11. Использование слишком много статических полей или методов может привести к затруднению тестирования и снижению гибкости кода. В большинстве случаев статические члены следует использовать осторожно, ограничивая их использование там, где это действительно необходимо.

[[Singleton]]