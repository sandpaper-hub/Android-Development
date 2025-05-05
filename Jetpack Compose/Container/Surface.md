В Jetpack Compose `Surface` и `Card` — это два близких по идее контейнера из библиотеки Material, но с разными целями и наборами настроек “из коробки”.

---

## 🔹 Surface

**Surface** — это самый базовый «холст» Material:

- **Фон и цвет контента**
    
    - `color` — по умолчанию `MaterialTheme.colorScheme.surface` (M3) или `MaterialTheme.colors.surface` (M2).
        
    - `contentColor` — подбирается автоматически (обычно `onSurface`/`onSurfaceVariant`).
        
- **Elevation**
    
    - `tonalElevation` (M3) — слегка тонирует фон, показывая «поднятую» поверхность.
        
    - `shadowElevation` (M3) или `elevation` (M2) — рисует тень под контейнером.
        
- **Форма (shape)**  
    Любая реализация `Shape` — `RoundedCornerShape`, `CircleShape` или своя.
    
- **Рамка (border)**  
    `BorderStroke` — рисует линию заданной толщины и цвета вокруг Surface.
    
- **Когда использовать**  
    — Когда нужен просто цветной фон с elevation/формой/рамкой, без привязки к дизайн-паттерну «карточка».  
    — В качестве базового блока для более сложных компонентов.
    

```kotlin
@Composable
fun MySurfaceExample() {
    Surface(
        modifier = Modifier
            .padding(16.dp)
            .fillMaxWidth(),
        shape = RoundedCornerShape(12.dp),
        tonalElevation = 2.dp,
        shadowElevation = 6.dp,
        color = MaterialTheme.colorScheme.surfaceVariant,
        contentColor = MaterialTheme.colorScheme.onSurfaceVariant,
        border = BorderStroke(1.dp, Color.Gray)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Text("Заголовок", style = MaterialTheme.typography.titleLarge)
            Text("Контент внутри Surface")
        }
    }
}

```