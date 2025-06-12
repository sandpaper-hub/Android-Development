**`OutlinedButton`** — это кнопка с прозрачным фоном и рамкой (border), которая служит для обозначения действий с низким или средним приоритетом, но всё же требующих акцента.

Используйте её, когда вам нужно подчеркнуть действие, но не так сильно, как при помощи заполненной (`Button`) или поднятой (`ElevatedButton`) кнопки. Например, «Отмена», «Подробнее» рядом с основным действием.

### **Базовый синтаксис:**
```kotlin
@Composable
fun SimpleOutlinedButton() {
    OutlinedButton(onClick = { /* действие */ }) {
        Text("Outlined")
    }
}
```

### Параметры:

- `onClick: () -> Unit` — обязательный, вызывается при нажатии.
    
- `modifier: Modifier` — для отступов, размеров, выравнивания.
    
- `enabled: Boolean` — по умолчанию `true` (отключает тень и рамку при `false`).
    
- `shape: Shape` — форма кнопки (углы, срезы).
    
- `colors: ButtonColors` — цвета текста и рамки.
    
- `border: BorderStroke` — толщина и цвет рамки.
    
- `contentPadding: PaddingValues` — внутренние отступы.

### Стандартный внешний вид

По умолчанию `OutlinedButton` в Material 3 использует:

- **Цвет рамки**: `colorScheme.outline` (обычно 1 dp, полупрозрачный).
    
- **Цвет текста (контента)**: `colorScheme.onSurfaceVariant`.
    
- **Фон**: прозрачный.
    
- **Тонкая рамка** вокруг контента.

### Кастомизация

1. Цвета
```kotlin
val customColors = ButtonDefaults.outlinedButtonColors(
    contentColor = Color(0xFF6200EE),      // цвет текста/иконки
    disabledContentColor = Color.Gray
)

OutlinedButton(
    onClick = { /*...*/ },
    colors = customColors
) {
    Text("Custom Colors")
}
```

2. Форма
```kotlin
val customColors = ButtonDefaults.outlinedButtonColors(
    contentColor = Color(0xFF6200EE),      // цвет текста/иконки
    disabledContentColor = Color.Gray
)

OutlinedButton(
    onClick = { /*...*/ },
    colors = customColors
) {
    Text("Custom Colors")
}
```

3. Рамка
```kotlin
OutlinedButton(
    onClick = {},
    border = BorderStroke(2.dp, Color.Magenta)
) {
    Text("Thick Border")
}
```

4. Отступы
```kotlin
OutlinedButton(
    onClick = {},
    contentPadding = PaddingValues(horizontal = 32.dp, vertical = 12.dp)
) {
    Text("Padded Button")
}
```

### Комбинация с иконкой и текстом
```kotlin
OutlinedButton(onClick = { /*...*/ }) {
    Icon(
        imageVector = Icons.Default.Info,
        contentDescription = "Info",
        modifier = Modifier.size(18.dp)
    )
    Spacer(modifier = Modifier.width(8.dp))
    Text("Подробнее")
}
```

### Интеграция с темой
```kotlin
@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    val colors = lightColorScheme(
        outline = Color(0xFF00796B),
        onSurfaceVariant = Color(0xFF004D40),
        // … другие цвета
    )

    MaterialTheme(
        colorScheme = colors,
        typography = Typography(),
        content = content
    )
}
```