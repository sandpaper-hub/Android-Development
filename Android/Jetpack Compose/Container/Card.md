**Card** — это специализированный `Surface` под Material-карточку:

- **Преднастроенные значения**
    
    - Форма: обычно `RoundedCornerShape(12.dp)` или значение из темы (`MaterialTheme.shapes.medium`).
        
    - Elevation: стандартное небольшое значение (1–3 dp в M2, 1–2 dp в M3).
        
    - В M3 есть `CardDefaults.cardElevation()` и `CardDefaults.cardColors()` для удобной темы.
        
- **onClick-перегрузка**  
    Есть версия `Card(onClick = { … })`, которая сама внутри сделает `Modifier.clickable()`, подключит ripple-эффект и роль `Role.Button`.
    
- **Когда использовать**  
    — Для визуального выделения блоков контента (товар, новость, профиль).  
    — Когда нужна кликабельная карточка с ripple и elevation по гайдлайну.

```kotlin
@Composable
fun MyCardExample() {
    Card(
        modifier = Modifier
            .padding(16.dp)
            .fillMaxWidth(),
        onClick = { /* обработка клика */ },
        shape = RoundedCornerShape(16.dp),
        elevation = CardDefaults.cardElevation(defaultElevation = 8.dp),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.primaryContainer,
            contentColor = MaterialTheme.colorScheme.onPrimaryContainer
        )
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Text("Заголовок карточки", style = MaterialTheme.typography.titleMedium)
            Spacer(Modifier.height(8.dp))
            Text("Описание внутри карточки.")
        }
    }
}

```

