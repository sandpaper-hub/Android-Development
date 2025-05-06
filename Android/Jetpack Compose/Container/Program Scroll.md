В Jetpack Compose ты можешь прокручивать списки и любые другие прокручиваемые контейнеры «программно» — то есть из кода, без участия жеста пользователя. Для этого используются специальные объекты состояния прокрутки и их методы. Рассмотрим два самых распространённых случая.

---

## 1. Программная прокрутка обычных контейнеров (`verticalScroll` / `horizontalScroll`)

Когда ты делаешь, например,

```kotlin
val scrollState = rememberScrollState()
Column(modifier = Modifier.verticalScroll(scrollState)) {
    // много контента
}
```

— `scrollState` хранит текущее смещение в пикселях. Два ключевых метода:

- **`scrollState.animateScrollTo(value: Int)`**  
    Анимированно прокрутит до абсолютного смещения `value` (в пикселях).
    
- **`scrollState.scrollTo(value: Int)`**  
    Мгновенно (без анимации) установит смещение.
    

Поскольку оба метода — `suspend`, их нужно вызывать внутри `CoroutineScope`. Например:

```kotlin
val scrollState = rememberScrollState()
val scope = rememberCoroutineScope()

Button(onClick = {
    scope.launch {
        // Прокрутить на 200 пикселей вниз с анимацией
        scrollState.animateScrollTo(200)
    }
}) {
    Text("Прокрутить вниз")
}

Column(modifier = Modifier.verticalScroll(scrollState)) {
    // ...
}
```

---

## 2. Программная прокрутка ленивых списков (`LazyColumn` / `LazyRow`)

Для ленивых списков используется `LazyListState`, который хранит два свойства:

- `firstVisibleItemIndex` — индекс первого видимого элемента.
    
- `firstVisibleItemScrollOffset` — смещение внутри этого элемента в пикселях.
    

### Создание и привязка состояния

```kotlin
val listState = rememberLazyListState()

LazyColumn(state = listState) {
    items(myItems, key = { it.id }) { item ->
        /* ... */
    }
}
```

### Методы прокрутки

- **`listState.animateScrollToItem(index: Int, scrollOffset: Int = 0)`**  
    Анимированно прокрутит так, чтобы элемент с данным `index` оказался вверху (+смещение).
    
- **`listState.scrollToItem(index: Int, scrollOffset: Int = 0)`**  
    Мгновенно переместит список.
    

Оба метода — тоже `suspend`, поэтому:

```kotlin
val scope = rememberCoroutineScope()

Button(onClick = {
    scope.launch {
        // Прокрутить так, чтобы 10-й элемент оказался сверху
        listState.animateScrollToItem(index = 10)
    }
}) {
    Text("Перейти к Item #10")
}
```

---

## 3. Программная прокрутка сеток и staggered-grid

Для `LazyVerticalGrid`, `LazyVerticalStaggeredGrid` и им подобных всё то же:

- Используешь `rememberLazyGridState()` или `rememberLazyStaggeredGridState()`.
    
- В них есть те же методы `.animateScrollToItem(...)` и `.scrollToItem(...)`.
    

```kotlin
val gridState = rememberLazyGridState()
LazyVerticalGrid(columns = GridCells.Fixed(2), state = gridState) {
    items(myItems) { /* ... */ }
}

LaunchedEffect(Unit) {
    // сразу после компоновки
    gridState.scrollToItem(5)
}
```

---

## 4. Полезные советы

- **Вызывать из `LaunchedEffect`**, если нужно прокрутить «при загрузке» экрана.
    
- **Использовать `animateScrollTo…`** для плавности.
    
- **Следить за состоянием**: можно подписаться на `snapshotFlow { listState.firstVisibleItemIndex }` и реагировать на изменение позиции.
    
- **Не блокировать главный поток**: все методы `suspend` должны вызываться в корутине.
    

---

Таким образом, программная прокрутка в Compose сводится к двум шагам:

1. **Получить/запомнить** нужный `ScrollState` или `LazyListState`.
    
2. **Вызвать** `scrollTo…` или `animateScrollTo…` внутри корутины.
    

Это позволяет создавать «скролл-кнопки», автопрокрутку при загрузке, возврат к заголовку и любые другие сценарии управления прокруткой из кода.