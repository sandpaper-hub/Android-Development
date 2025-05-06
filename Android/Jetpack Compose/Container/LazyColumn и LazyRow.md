**LazyColumn / LazyRow** и **Column / Row** в Jetpack Compose служат для схожей задачи — компоновки элементов по вертикали или горизонтали — но работают по-разному и предназначены для разных сценариев.

---

## 1. Ленивость (виртуализация)

- **LazyColumn / LazyRow**
    
    - Отображают **только** те элементы, которые видны на экране (и немного за его пределами).
        
    - Под капотом используют механизм «виртуализации» списка, аналогичный RecyclerView: создают и компонуют View/Composables по мере необходимости.
        
    - Хорошо подходят для **длинных** или **динамических** списков (тысячи элементов).
        
- **Column / Row**
    
    - Компонуют **все** дочерние элементы сразу, независимо от того, видны они или нет.
        
    - При большом количестве элементов это может приводить к падению производительности и OOM.
        

---

## 2. Скроллинг

- **LazyColumn / LazyRow**
    
    - Встроенный скроллинг: достаточно разместить их без дополнительных модификаторов.
        
    - Управление положением прокрутки через `rememberLazyListState()` и `listState.firstVisibleItemIndex`, `animateScrollToItem()` и т.п.
        
- **Column / Row**
    
    - Не умеют скроллиться сами по себе.
        
    - Чтобы сделать прокручиваемый список, нужно оборачивать в `verticalScroll(rememberScrollState())` или `horizontalScroll(...)`.
        
    - При этом все элементы по-прежнему создаются сразу и лежат в памяти.
        

---

## 3. DSL и API

- **LazyColumn / LazyRow**
    
    ```kotlin
    LazyColumn {
      // одиночный элемент
      item { Text("Заголовок") }
    
      // список по коллекции
      items(myList, key = { it.id }) { item ->
        ListItem(item)
      }
    }
    ```
    
    - Методы `item {}` и `items(...) {}` внутри блока дают гибкость: ключи, заголовки, разделители, sticky-headers.
        
- **Column / Row**
    
    ```kotlin
    Column {
      Text("Заголовок")
      myList.forEach { item ->
        ListItem(item)
      }
    }
    ```
    
    - Обычная декларативная компоновка, без поддержки ключей или ленивой загрузки.
        

---

## 4. Управление состоянием и анимации

- **LazyColumn / LazyRow**
    
    - Сохраняют позицию прокрутки между перекомпоновками (через `rememberLazyListState()`).
        
    - Позволяют программно скроллить к конкретному элементу с анимацией.
        
- **Column / Row + Scroll**
    
    - Используют `rememberScrollState()`, но в целом прокрутка менее «умная»: нет индексов элементов, только смещение в пикселях.
        

---

## 5. Когда что использовать

|Сценарий|Column / Row + Scroll|LazyColumn / LazyRow|
|---|---|---|
|**Небольшой статичный набор**|✔ быстро и просто|✗ лишняя абстракция|
|**Длинный/бесконечный список**|✗ создаст OOM и лаги|✔ производительно и лениво|
|**Sticky headers / ключи / диапазоны**|– нет поддержки|✔ есть `stickyHeader`, `key`|
|**Программный скролл к элементу**|– только пиксели|✔ можно `animateScrollToItem`|
|**Комбинация с другими DSL**|✔ гибко|✔ но внутри LazyScope|

---

### Вывод

- **Используйте `Column`/`Row`** для небольших наборов и когда вам просто нужна статичная компоновка.
    
- **Используйте `LazyColumn`/`LazyRow`** для списков средней и большой длины, когда важна производительность, ленивость и удобство работы с элементами по индексу.

### Примеры кода
## LazyColumn

```kotlin
@Composable
fun SimpleLazyColumn() {
    // Пример данных
    val items = List(20) { "Пункт #$it" }

    LazyColumn(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp),       // отступы между элементами
        contentPadding = PaddingValues(vertical = 8.dp)         // отступы сверху/снизу всего списка
    ) {
        items(items) { item ->
            Text(
                text = item,
                modifier = Modifier
                    .fillMaxWidth()
                    .background(Color(0xFFE0E0E0), RoundedCornerShape(4.dp))
                    .padding(12.dp)
            )
        }
    }
}

```

## LazyRow
```kotlin
@Composable
fun SimpleLazyRow() {
    // Пример данных
    val items = List(10) { "Элемент $it" }

    LazyRow(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),    // отступы между элементами
        contentPadding = PaddingValues(horizontal = 8.dp)      // отступы слева/справа всего ряда
    ) {
        items(items) { item ->
            Box(
                modifier = Modifier
                    .size(width = 120.dp, height = 80.dp)
                    .background(Color(0xFFBBDEFB), RoundedCornerShape(8.dp)),
                contentAlignment = Alignment.Center
            ) {
                Text(text = item)
            }
        }
    }
}

```