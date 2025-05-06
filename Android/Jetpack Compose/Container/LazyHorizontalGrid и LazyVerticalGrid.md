**`LazyGrid`** в Jetpack Compose — это ленивый контейнер для компоновки элементов в виде сетки (grid), который рендерит только видимые на экране ячейки и работает по той же идеологии, что и `LazyColumn`/`LazyRow`. В настоящее время в Compose есть два основных варианта:

- **`LazyVerticalGrid`** — прокручиваемая по вертикали сетка (ряды тянутся слева направо, а колонки — сверху вниз).
    
- **`LazyHorizontalGrid`** — прокручиваемая по горизонтали сетка (колонки тянутся сверху вниз, а ряды — слева направо).
    

---

## Основной API

```kotlin
@Composable
fun LazyVerticalGrid(
    columns: GridCells,
    modifier: Modifier = Modifier,
    state: LazyGridState = rememberLazyGridState(),
    contentPadding: PaddingValues = PaddingValues(0.dp),
    verticalArrangement: Arrangement.Vertical = Arrangement.Top,
    horizontalArrangement: Arrangement.Horizontal = Arrangement.Start,
    content: LazyGridScope.() -> Unit
)
```

- **`columns: GridCells`** — обязательный параметр. Определяет, на сколько «столбцов» разбить экран:
    
    - `GridCells.Fixed(count: Int)` — фиксированное число столбцов.
        
    - `GridCells.Adaptive(minSize: Dp)` — количество столбцов подбирается автоматически так, чтобы ширина ячейки была не менее `minSize`.
        
- **`state`** — позволяет программно управлять и отслеживать прокрутку (через `rememberLazyGridState()` и его методы `firstVisibleItemIndex`, `firstVisibleItemScrollOffset`, `animateScrollToItem()` и т.д.).
    
- **`contentPadding`** — внутренние отступы вокруг всей сетки (top, bottom, start, end).
    
- **`verticalArrangement`** и **`horizontalArrangement`** — распределение свободного пространства между строками и столбцами.
    
- **`content: LazyGridScope.() -> Unit`** — DSL, внутри которого:
    
    - `item { … }` — одиночная ячейка.
        
    - `items(list) { … }` / `items(count) { index -> … }` — несколько ячеек на основе коллекции или индекса.
        
    - Можно использовать `span` в `LazyGridScope` (для объединения ячеек по ширине).
        

---

## Пример `LazyHorizontalGrid`

```kotlin
@Composable  
@Suppress("FunctionName")  
fun LazyHorizontalGridExample() {  
    val numbersList = (0..12).toList()  
  
    Column(  
        modifier = Modifier  
            .padding(12.dp).fillMaxWidth().border(  
                2.dp,  
                MaterialTheme.colorScheme.primaryContainer,  
                shape = RoundedCornerShape(8.dp)  
            ).padding(16.dp)  
    ) {  
        Text("LazyHorizontalGrid example")  
        LazyHorizontalGrid(  
            rows = GridCells.Fixed(3), modifier = Modifier  
                .fillMaxWidth().height(200.dp)  
        ) {  
            items(numbersList) { number ->  
                Card(  
                    modifier = Modifier  
                        .size(100.dp, 60.dp)  
                        .padding(8.dp),  
                    shape = RoundedCornerShape(8.dp),  
                    elevation = CardDefaults.cardElevation(defaultElevation = 8.dp),  
                    colors = CardDefaults.cardColors(  
                        contentColor = MaterialTheme.colorScheme.onPrimaryContainer,  
                        containerColor = MaterialTheme.colorScheme.primaryContainer  
                    )  
                ) {  
                    Box(  
                        modifier = Modifier  
                            .fillMaxSize(),  
                        contentAlignment = Alignment.Center  
                    ) {  
                        Text("Item $number", fontSize = 20.sp, modifier = Modifier.padding(8.dp))  
                    }  
                }            }        }    }}
```

## Пример `LazyVerticalGrid`
```kotlin
@Composable  
@Suppress("FunctionName")  
fun LazyVerticalGridExample() {  
    val numberList = (0..12).toList()  
  
    Column(  
        modifier = Modifier  
            .padding(12.dp).fillMaxWidth().border(  
                2.dp, MaterialTheme.colorScheme.primaryContainer,  
                shape = RoundedCornerShape(8.dp)  
            ).padding(16.dp)  
    ) {  
        Text("LazyVerticalGrid example")  
        LazyVerticalGrid(  
            columns = GridCells.Fixed(3), modifier = Modifier  
                .fillMaxWidth().height(160.dp)  
        ) {  
            items(numberList) { number ->  
                Card(  
                    modifier = Modifier  
                        .size(100.dp, 60.dp)  
                        .padding(8.dp),  
                    shape = RoundedCornerShape(8.dp),  
                    elevation = CardDefaults.cardElevation(defaultElevation = 8.dp),  
                    colors = CardDefaults.cardColors(  
                        contentColor = MaterialTheme.colorScheme.onPrimaryContainer,  
                        containerColor = MaterialTheme.colorScheme.primaryContainer  
                    )  
                ) {  
                    Box(  
                        modifier = Modifier  
                            .fillMaxSize(),  
                        contentAlignment = Alignment.Center  
                    ) {  
                        Text("Item $number", fontSize = 20.sp, modifier = Modifier.padding(8.dp))  
                    }  
                }
            } 
		} 
	}
}
```

---

## Специальные возможности

1. **`span`** — внутри `items` можно объединить несколько «столбцов» в одну ячейку:
    
    ```kotlin
    LazyVerticalGrid(columns = GridCells.Fixed(3)) {
      item(span = { GridItemSpan(3) }) {
        Text("Заголовок на всю ширину")
      }
      items(myList) { item ->
        // каждая ячейка занимает 1 столбец
      }
    }
    ```
    
2. **Программный скролл**:
    
    ```kotlin
    val scope = rememberCoroutineScope()
    Button(onClick = {
      scope.launch {
        gridState.animateScrollToItem(index = 20)
      }
    }) {
      Text("Перейти к 21-му фото")
    }
    ```
    
3. **Sticky headers**  
    Пока нет встроенных «липких» заголовков, но можно вручную:
    
    - Сделать первый элемент в секции с `span = { GridItemSpan(maxLineSpan) }` и отслеживать прокрутку, чтобы условно рисовать заголовок вне сетки.
        
4. **LazyHorizontalGrid**  
    Почти зеркальный API — задаёте `rows = GridCells.Fixed(...)` или `Adaptive`, и внутри используете `horizontalArrangement`/`verticalArrangement`.
    

---

## Когда использовать

- **Плиточные интерфейсы**: галереи, каталоги, меню иконок, карточки товаров.
    
- **Адаптивный layout**: сетка, количество столбцов которой подстраивается под размер экрана.
    
- **Большие наборы данных**: сотни и тысячи элементов, где важно не рендерить всё сразу.
    

---

### Итог

- **LazyVerticalGrid** и **LazyHorizontalGrid** — ленивые сетки, рендерящие только видимые ячейки.
    
- **GridCells.Fixed/Adaptive** — фиксированный или адаптивный размер/количество колонок (или строк).
    
- Поддерживают `state`, `item`/`items`, `span`, `contentPadding` и `Arrangement`.
    
- Позволяют создавать производительные и адаптивные «плиточные» интерфейсы в Compose.