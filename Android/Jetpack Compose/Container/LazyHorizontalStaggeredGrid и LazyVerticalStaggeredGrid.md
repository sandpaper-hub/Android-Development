В Jetpack Compose (начиная с версии Compose 1.4.0 и выше) появились два «лёгких» контейнера для «ступенчатых» (staggered) сеток:

- **`LazyVerticalStaggeredGrid`** – вертикальная прокрутка, внутри которой строки состоят из ячеек разной высоты.
    
- **`LazyHorizontalStaggeredGrid`** – горизонтальная прокрутка, внутри которой столбцы состоят из ячеек разной ширины.
    

Они похожи на `LazyVerticalGrid`/`LazyHorizontalGrid`, но дают эффект Pinterest-стиля, когда элементы не ровняются по единому размеру.

---

## 1. Базовый API

```kotlin
@Composable
fun LazyVerticalStaggeredGrid(
    columns: StaggeredGridCells,                // схема колонок
    modifier: Modifier = Modifier,
    state: LazyStaggeredGridState = rememberLazyStaggeredGridState(),
    contentPadding: PaddingValues = PaddingValues(0.dp),
    horizontalArrangement: Arrangement.Horizontal = Arrangement.spacedBy(0.dp),
    verticalItemSpacing: Dp = 0.dp,
    content: LazyStaggeredGridScope.(Int) -> Unit
)
```

И аналогично для **`LazyHorizontalStaggeredGrid`** с параметром `rows: StaggeredGridCells` и `verticalArrangement`/`horizontalItemSpacing`.

### `StaggeredGridCells`

- `Fixed(count: Int)` — фиксированное число столбцов (для вертикальной) или строк (для горизонтальной).
    
- `Adaptive(minSize: Dp)` — подбирает число “колонок”/“строк”, чтобы размер ячейки был не меньше `minSize`.
    

---

## 2. Пример вертикальной ступенчатой сетки

```kotlin
@Composable  
@Suppress("FunctionName")  
fun LazyVerticalStaggeredGridExample() {  
    val numbers = (0..15).toList()  
  
    Column(  
        modifier = Modifier  
            .padding(12.dp)  
            .fillMaxWidth()  
            .height(300.dp)  
            .border(2.dp, MaterialTheme.colorScheme.primaryContainer, RoundedCornerShape(8.dp))  
            .padding(16.dp)  
    ) {  
        Text("LazyVerticalStaggeredGrid Example")  
        LazyVerticalStaggeredGrid(  
            columns = StaggeredGridCells.Fixed(2),  
            modifier = Modifier.fillMaxSize(),  
            contentPadding = PaddingValues(8.dp),  
            horizontalArrangement = Arrangement.spacedBy(8.dp),  
            verticalItemSpacing = 8.dp  
        ) {  
            items(numbers, key = { it }) { number ->  
                val elementHeight = (50 + (0..150).random())  
                Box(  
                    modifier = Modifier  
                        .fillMaxWidth()  
                        .height(elementHeight.dp)  
                        .background(  
                            MaterialTheme.colorScheme.primaryContainer,  
                            RoundedCornerShape(8.dp)  
                        )                        .padding(8.dp),  
                    contentAlignment = Alignment.Center  
                ) {  
                    Text("$number element\n$elementHeight dp")  
                }  
            }        
        }    
    }
}
```

- Здесь каждый элемент получает свою высоту, и сетка «усаживает» их в две колонки, заполняя сначала колонку 1, затем — более высокую / низкую, чтобы общая высота колонок была примерно равна.
    

---

## 3. Пример горизонтальной ступенчатой сетки

```kotlin
@Composable  
@Suppress("FunctionName")  
fun LazyHorizontalStaggeredGridExample() {  
    val numbers = (0..15).toList()  
  
    Column(  
        modifier = Modifier  
            .padding(12.dp)  
            .fillMaxWidth()  
            .height(300.dp)  
            .border(2.dp, MaterialTheme.colorScheme.primaryContainer, RoundedCornerShape(8.dp))  
            .padding(16.dp)  
    ) {  
        Text("LazyHorizontalStaggeredGrid Example")  
        LazyHorizontalStaggeredGrid(  
            rows = StaggeredGridCells.Fixed(2),  
            modifier = Modifier.fillMaxSize(),  
            contentPadding = PaddingValues(8.dp),  
            verticalArrangement = Arrangement.spacedBy(8.dp),  
            horizontalItemSpacing = 8.dp  
        ) {  
            items(numbers, key = { it }) { number ->  
                val elementWidth = (50 + (0..150).random())  
                Box(  
                    modifier = Modifier  
                        .fillMaxHeight()  
                        .width(elementWidth.dp)  
                        .background(  
                            MaterialTheme.colorScheme.primaryContainer,  
                            RoundedCornerShape(8.dp)  
                        )                        .padding(8.dp),  
                    contentAlignment = Alignment.Center  
                ) {  
                    Text("$number element\n$elementWidth dp")  
                }  
            }        }    }}
```

- Элементы получают разную **ширину**, а две строки «через одну» заполняются, как мозаика.
    

---

## 4. Управление состоянием и программный скролл

```kotlin
val gridState = rememberLazyStaggeredGridState()

LazyVerticalStaggeredGrid(
    columns = StaggeredGridCells.Fixed(2),
    state = gridState,
    ...
) {
    items(count = items.size) { index -> /* ... */ }
}

// Программный скролл
LaunchedEffect(Unit) {
    gridState.animateScrollToItem(index = 20)
}
```

- `LazyStaggeredGridState` содержит `firstVisibleItemIndex` и `firstVisibleItemScrollOffset` точно так же, как в обычных `LazyColumn`.
    

---

## 5. Когда использовать

- **Фото-галереи** с разным соотношением сторон (Pinterest-эффект).
    
- **Карточки товаров** или плитки, у которых контент разной высоты (описание, картинки).
    
- **Художественные коллажи**, ленты постов разного размера.
    

Если вам нужна **ровная сетка**, лучше взять `LazyVerticalGrid` / `LazyHorizontalGrid`. Для **ступенчатого** вида — используйте `LazyVerticalStaggeredGrid` / `LazyHorizontalStaggeredGrid`.

---

### Итог

- **LazyVerticalStaggeredGrid** и **LazyHorizontalStaggeredGrid** — ленивые сетки ступенчатого (masonry) типа.
    
- Задают разные размеры ячеек, «упаковывают» их в фиксированное или адаптивное число колонок/строк.
    
- Поддерживают state, contentPadding, spacing и программный скролл.
    
- Идеально подходят для визуализации элементов с переменным размером.