В Jetpack Compose (начиная с `androidx.compose.foundation:foundation` версии ~1.2.0) в `LazyColumn` появилась возможность делать «прилипающие» заголовки — **sticky headers**. Они остаются видимыми вверху экрана, пока не сменятся следующим заголовком своего же уровня.

---

## 🔧 Как включить

```kotlin
@OptIn(ExperimentalFoundationApi::class)
@Composable
fun MyListWithStickyHeaders() {
    LazyColumn {
        /* ... */
    }
}
```

Пока что API отмечено как экспериментальное, поэтому нужно объявить `@OptIn(ExperimentalFoundationApi::class)`.

---

## 📐 Сигнатура

Внутри DSL `LazyListScope` доступны два новых блока:

- `stickyHeader { /* заголовок */ }`
    
- `stickyFooter { /* футер */ }` _(с `StickyListScope` в экспериментальных версиях)_
    

Но чаще используется именно `stickyHeader`.

---

## 📝 Пример группировки по первой букве

```kotlin
@OptIn(ExperimentalFoundationApi::class)
@Composable
fun ContactsStickyHeaders(contacts: List<String>) {
    // Группируем по первой букве
    val grouped = contacts.groupBy { it.first().uppercaseChar() }
        .toSortedMap()

    LazyColumn(modifier = Modifier.fillMaxSize()) {
        grouped.forEach { (letter, names) ->
            // Прилипающий заголовок
            stickyHeader {
                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .background(Color.LightGray)
                        .padding(8.dp)
                ) {
                    Text(
                        text = letter.toString(),
                        style = MaterialTheme.typography.titleMedium
                    )
                }
            }
            // Сами элементы группы
            items(names, key = { it }) { name ->
                Text(
                    text = name,
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(vertical = 4.dp, horizontal = 16.dp)
                )
            }
        }
    }
}
```

- Каждый **`stickyHeader`** рисуется один раз перед соответствующей группой.
    
- При скролле он «прилипает» к верхнему краю, пока не наступит следующий `stickyHeader`.
    

---

## 🎨 Настройка внешнего вида

- Можно внутри `stickyHeader` использовать любые `Modifier` — фон, elevation, `shadow()`, `border()`.
    
- Если хотите, чтобы между заголовком и списком был разделитель, вставьте `Divider()` прямо в `stickyHeader`.
    

```kotlin
stickyHeader {
  Column {
    Text(..., modifier = Modifier.background(Color.White))
    Divider(color = Color.Gray)
  }
}
```

---

## 🚩 Важные нюансы

1. **Производительность**  
    Sticky headers рендерятся как часть `LazyColumn` — тяжелыми они не становятся, но не забывайте про `key` у элементов.
    
2. **Nested scrolling**  
    Вложенные скроллы могут ломать «прилипание». Обычно `LazyColumn` с `stickyHeader` используется без вложенных вертикальных списков.
    
3. **Альтернатива**  
    Если вы используете более старую версию Compose или хотите гибкости — можно вручную отслеживать `firstVisibleItemIndex` и рисовать собственный заголовок поверх списка (например, в `Box` над `LazyColumn`).
    

---

### Итог

- **`stickyHeader { … }`** внутри `LazyColumn` позволяет закреплять заголовок группы на экране.
    
- Требует `@OptIn(ExperimentalFoundationApi::class)`.
    
- Отлично подходит для списков с группировкой (контакты, песни по алфавиту, чаты по дате).