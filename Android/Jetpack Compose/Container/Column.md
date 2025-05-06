`Column` в Jetpack Compose — это один из базовых контейнеров для компоновки, который укладывает своих дочерних @Composable‑элементов один под другим по вертикали. По сути, он аналогичен `LinearLayout` с ориентацией `vertical` в классическом View‑мире, но с гораздо более гибким API.

---

## Основные параметры

```kotlin
@Composable
fun Column(
    modifier: Modifier = Modifier,
    verticalArrangement: Arrangement.Vertical = Arrangement.Top,
    horizontalAlignment: Alignment.Horizontal = Alignment.Start,
    content: @Composable ColumnScope.() -> Unit
)
```

- **`modifier`**  
    Позволяет задавать размеры, отступы, фон, клики и т.д. (точно так же, как и в других контейнерах).
    
- **`verticalArrangement`**  
    Определяет, как распределять свободное вертикальное пространство между и вокруг детей.  
    Варианты:
    
    - `Arrangement.Top`, `Center`, `Bottom` — выравнивание всех детей к верхнему/центру/нижнему краю.
        
    - `Arrangement.spacedBy(16.dp)` — равные отступы по 16 dp между элементами.
        
    - `Arrangement.SpaceBetween`, `SpaceAround`, `SpaceEvenly` — распределяют свободное место между детьми по-разному.
        
- **`horizontalAlignment`**  
    Выравнивание детей по горизонтали внутри колонки:
    
    - `Alignment.Start`, `CenterHorizontally`, `End`
        
    - Если хотите, чтобы все элементы были по центру по ширине, укажите `horizontalAlignment = Alignment.CenterHorizontally`.
        
- **`content`**  
    Лямбда с приёмником `ColumnScope`, в которой вы описываете список дочерних элементов.
    

---

## Пример базового использования

```kotlin
Column(
  modifier = Modifier
      .fillMaxWidth()
      .padding(16.dp),
  verticalArrangement = Arrangement.spacedBy(8.dp),
  horizontalAlignment = Alignment.CenterHorizontally
) {
  Text("Заголовок", style = MaterialTheme.typography.titleLarge)
  Text("Описание под заголовком")
  Button(onClick = { /* ... */ }) {
    Text("Нажми меня")
  }
}
```

Здесь мы:

1. **Растянули** колонку по ширине экрана.
    
2. Добавили **отступы** 16 dp вокруг.
    
3. Между элементами ставим **8 dp**.
    
4. Все дети **центрированы по горизонтали**.
    

---

## Вес (Weight)

Если вы хотите, чтобы один из детей занял оставшееся пространство, можно использовать `Modifier.weight()` внутри `ColumnScope`:

```kotlin
Column(modifier = Modifier.fillMaxSize()) {
  Text("Шапка", modifier = Modifier.padding(16.dp))
  Box(
    modifier = Modifier
      .weight(1f)       // этот Box займет всё свободное пространство
      .fillMaxWidth()
      .background(Color.LightGray)
  )
  Text("Подвал", modifier = Modifier.padding(16.dp))
}
```

- **`weight(1f)`** обозначает «это элемент растягивается, чтобы заполнить всё доступное место».
    
- Можно задавать разные веса, например `weight(2f)` и `weight(1f)`, чтобы пропорционально распределить пространство.
    

---

## Column vs LazyColumn

- **`Column`** отрисовывает **все** дочерние элементы сразу. Подходит, когда элементов немного и их размер предсказуем.
    
- **`LazyColumn`** (из `foundation.lazy`) создаёт **ленивый список**, при котором компонуются и отображаются только видимые на экране элементы. Используйте `LazyColumn` для длинных списков или динамических данных (аналог RecyclerView).
    

---

## ColumnWithConstraints

Если внутри `Column` хочется подстраиваться под фактический размер контейнера, есть **`ColumnWithConstraints`**:

```kotlin
ColumnWithConstraints {
  if (maxHeight < 200.dp) {
    Text("Низкий контейнер")
  } else {
    Text("Высокий контейнер")
  }
}
```

Внутри вам доступны `maxWidth`, `maxHeight`, `minWidth`, `minHeight`.

---

### Итог

- **`Column`** — главный контейнер для вертикальной компоновки.
    
- **`verticalArrangement`** и **`horizontalAlignment`** управляют выравниванием и интервалами.
    
- **`Modifier.weight`** позволяет растягивать элементы на всё доступное место.
    
- Для больших списков вместо `Column` используйте **`LazyColumn`**.
    

С этой функциональностью вы можете строить как простой статичный UI, так и адаптивные макеты, которые гибко реагируют на размер экрана и содержимое.