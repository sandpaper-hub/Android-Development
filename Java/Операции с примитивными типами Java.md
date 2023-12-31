#переменные #тип_данных #Java 
Знак `=` — это оператор присваивания. Слева от знака `=` переменная, справа — её значение. Элементы, на которые воздействует оператор: переменные или конкретные значения, стоящие в коде рядом с оператором, называются операндами.

Слева от оператора присваивания может находиться только имя переменной, которой присваивается значение. Нельзя присвоить одно значение другому:

```Java
23 = 24; // так нельзя! 
```

Всё, что находится справа от двух слэшей `//`, не является исполняемым кодом и игнорируется компилятором. Разработчики часто используют эту опцию, чтобы оставлять в коде поясняющие комментарии или временно исключить какую-то часть кода.

Если две переменные имеют одинаковый тип, то значение одной переменной можно присвоить другой:

```Java
float targetWeight = 63.5f;
float currentWeight = 72.3f;

currentWeight = targetWeight; 
```

В результате выполнения последней строки кода в переменной `currentWeight` будет храниться 63.5.

### Арифметические операторы

В Java есть **12 арифметических операторов**.

- `+` — **оператор сложения.** Складывает операнды:

```Java
int a = 5 + 8; // переменной a будет присвоен результат сложения  5 и 8, а именно 13 
```

- `-` — **оператор вычитания.** Вычитает из левого операнда значение правого:

```java
int a = 4;
int b = 7;
b = a - b; // в результате переменной b будет присвоено значение -3 
```

- `*` — **оператор умножения.** Перемножает операнды:

```java
int c = 5 * 11; // с будет присвоено значение 55 
```

- `/` — **оператор деления.** Делит значение левого операнда на значение правого:

```java
float f = 5 / 2; // в данном случае переменной f будет присвоено значение 2 (без дробной части) 
```

Все целые числа в коде рассматриваются средой разработки как `int`. Результатом деления одного целого числа на другое целое число будет целое число, несмотря на тип переменной со значением `float`.

Если нам необходимо получить дробное значение как результат деления двух целых чисел, то нужно дать понять среде разработки, что мы хотим работать с этими числами как с дробными. Сделать это можно используя постфикс `f`:

```java
float f = 5f / 2f; // в этом случае f будет равняться 2.5 
```

- `%` — оператор деления по модулю — специальная арифметическая операция для нахождения остатка от деления одного целого числа на другое. Результатом этой операции будет остаток от деления левого операнда на правый:

```java
int result = 5 % 2; // результатом будет остаток от деления 5 на 2, а именно 1 
```

Важно понимать, что операторы сложения, вычитания, умножения, деления и деления по модулю не изменяют операнды. Они производят вычисления, результат которых обязательно нужно присвоить какой-то переменной. И если вы попытаетесь написать код такого вида:

```Java
int a = 5, b = 13;
a / b; 
```

Среда разработки сразу же сообщит вам об ошибке, поскольку результат вычисления ничему не присваивается. Правильно будет эту операцию присвоить, например, какой-то другой переменной:

```java
int a = 26, b = 13;
int c = a / b; 
```

Однако следующие два оператора являются исключением из этого правила. `++` — инкремент. Этот оператор применяется к переменной, и всё, что он делает, — увеличивает значение переменной на 1:

```java
int a = 2, b;
a++;
b = a; // после выполнения предыдущей строки кода значение переменной a было увеличено на 1. Соответственно, переменной b будет присвоено значение 3. 
```

При выполнении инкремента `a++` происходит следующее:

```
a = a + 1; 
```

Результат сложения присваивается самой переменной. Поэтому использование инкремента без присваивания какой-то другой переменной не является ошибкой.

Это очень популярный оператор, который часто используется в решении рабочих задач, в будущем вы в этом убедитесь.

Представленный вид инкремента называется **постфиксным**, в нём оператор расположен справа от переменной. Существует и **префиксный** инкремент, и записывается он следующим образом:

```java
++a; 
```

В результате переменная `a` будет увеличена на 1, но если попробовать использовать инкремент при присваивании значения другой переменной, то можно увидеть результат:

```java
int a = 4, b; 
```

Попробуем присвоить переменной `b` значение следующим образом:

```java
b = a++; // в результате выполнения этой строки переменной `b` будет присвоено значение переменной `a` до увеличения на 1 (то есть 4), и лишь после этого a будет увеличена на 1. 
```

Если присвоить значение следующим образом:

```java
b = ++a; // то сперва значение a будет увеличено на 1, а только потом присвоено переменной b.
```

- `--` — декремент. Операция, обратная инкременту, уменьшает значение переменной на 1. Записывается следующим образом:

```java
a--;
--a; 
```

### Операции с присваиванием

В Java для операций `+`, `-`, `*`, `/` и `%` существуют соответствующие операции «с присваиванием».

Представим, что нам нужно увеличить значение переменной `b` на 3. Мы можем сделать это следующим образом:

```java
int b = 4;
b = b + 3; // в результате b будет равняться 7 
```

В процессе разработки подобные операции приходится производить часто. И для упрощения жизни разработчикам были придуманы специальные операторы, которые позволяют сократить код и получить тот же результат:

```java
int b = 4;
b += 3; // в результате b будет присвоено 7 
```

и, соответственно:

```java
int b = 4;
b *= 5; // b будет присвоено значение 20 
```

Называются такие операторы:

- `+=` — сложение с присваиванием

- `-=` — вычитание с присваиванием

- `*=` — умножение с присваиванием

- `/=` — деление с присваиванием

- `%=` — деление по модулю с присваиванием

Все эти операторы призваны упростить выражения, в которых необходимо изменить исходное значение переменной какой-то простой арифметической операцией.

Подобные операции можно производить не только с конкретными значениями, но и с другими переменными:

```java
int a = 7, b = 2;
a %= b; // в результате переменной a будет присвоено значение 1, поскольку остаток от деления 7 на 2 — это 1 
```

или даже с самой исходной переменной:

```java
int a = 7;
a *= a; // в результате переменная будет умножена сама на себя и получит значение 49 
```
### Приоритет операций

Разумеется, в одной строчке кода результатом присваивания может быть выполнение операций не только с двумя числами или переменными:

```java
int a = 6, b = 3, c = 2, d;
d = a / b + b * c - a; //  результат 2 
```

В коде можно выстраивать полноценные уравнения. Приоритет выполнения операций работает точно как в математике:

- **операции выполняются слева направо;**
- **сперва выполняются умножение, деление и деление по модулю;**
- **затем выполняются сложения и вычитания.**

Естественно, если в выражении есть скобки, то в первую очередь выполняются операции в скобках:

```java
int a = 6, b = 3, c = 2, d;
d = a / b + b * (c - a); // результат -10 
```