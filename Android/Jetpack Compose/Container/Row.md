В Jetpack Compose `Row` — это базовый контейнер для горизонтальной компоновки: все его дочерние элементы располагаются слева направо (аналог `LinearLayout` с ориентацией `horizontal` в классическом View-мире). Вот как его можно использовать и настраивать.

---

## Сигнатура

```kotlin
@Composable
fun Row(
    modifier: Modifier = Modifier,
    horizontalArrangement: Arrangement.Horizontal = Arrangement.Start,
    verticalAlignment: Alignment.Vertical = Alignment.Top,
    content: @Composable RowScope.() -> Unit
)
```

- **`modifier`**  
    Любые модификаторы (`padding`, `size`, `background`, `clickable` и т. д.).
    
- **`horizontalArrangement`**  
    Распределение свободного пространства **между** и **вокруг** детей по горизонтали:
    
    - `Arrangement.Start`, `Center`, `End` — всё выравнивание к краю или центру.
        
    - `Arrangement.spacedBy(16.dp)` — фиксированный отступ 16 dp между элементами.
        
    - `Arrangement.SpaceBetween` / `SpaceAround` / `SpaceEvenly` — распределяют оставшееся пространство разными способами.
        
- **`verticalAlignment`**  
    Выравнивание по вертикали внутри `Row`:
    
    - `Alignment.Top`, `CenterVertically`, `Bottom`.
        
- **`content` (RowScope)**  
    Лямбда, в которой вы описываете дочерние элементы. Внутри области действия `RowScope` доступны вспомогательные методы (например, `Modifier.weight()`).
    

---

## Простой пример

```kotlin
Row(
  modifier = Modifier
    .fillMaxWidth()
    .padding(16.dp),
  horizontalArrangement = Arrangement.spacedBy(8.dp),
  verticalAlignment = Alignment.CenterVertically
) {
  Icon(Icons.Default.Favorite, contentDescription = null)
  Text("Like")
  Button(onClick = {}) {
    Text("Click me")
  }
}
```

Здесь:

- `fillMaxWidth()` растягивает `Row` на всю ширину.
    
- `spacedBy(8.dp)` вставляет 8 dp между детьми.
    
- `CenterVertically` выравнивает все элементы по центру по высоте.
    

---

## Распределение пространства: `weight`

Если вам нужно, чтобы один из элементов занял всё оставшееся пространство, используйте `Modifier.weight()`:

```kotlin
Row(modifier = Modifier.fillMaxWidth().height(56.dp)) {
  Text(
    "Имя пользователя",
    modifier = Modifier
      .weight(1f)            // займет всё доступное место
      .padding(start = 16.dp)
  )
  IconButton(onClick = {}) {
    Icon(Icons.Default.Settings, contentDescription = null)
  }
}
```

- `weight(1f)` заставляет текстовый элемент растягиваться, а кнопка займет только тот размер, который ей нужен.
    

---

## Альтернатива для больших списков: `LazyRow`

Если у вас длинный или динамический список элементов — используют **`LazyRow`**, который создаёт только видимые элементы, экономя память и ресурсы:

```kotlin
LazyRow(
  modifier = Modifier.fillMaxWidth(),
  horizontalArrangement = Arrangement.spacedBy(12.dp)
) {
  items(photoList) { photo ->
    Image(…, modifier = Modifier.size(100.dp))
  }
}
```

---

## Адаптивная компоновка: `RowWithConstraints`

Иногда нужно адаптироваться под доступный размер. Для этого есть `RowWithConstraints`:

```kotlin
RowWithConstraints {
  if (maxWidth < 300.dp) {
    // компактная компоновка
  } else {
    // расширенная компоновка
  }
}
```

Внутри доступны `maxWidth`, `maxHeight`, `minWidth`, `minHeight`.

---

### Итог

- **Row** — размещает детей горизонтально.
    
- **horizontalArrangement** и **verticalAlignment** управляют выравниванием и интервалами.
    
- **Modifier.weight** позволяет детям растягиваться.
    
- Для списков используйте **LazyRow**, для адаптации по размеру — **RowWithConstraints**.
    

Сочетание этих возможностей делает `Row` чрезвычайно гибким инструментом для любых горизонтальных макетов в Jetpack Compose.