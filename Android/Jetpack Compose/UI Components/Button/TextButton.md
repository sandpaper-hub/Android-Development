**`TextButton`** — это минималистичный вариант кнопки в Material 3 для Jetpack Compose. Он отображает лишь текст (или иконку без фона и рамки), идеально подходит для самых низкоприоритетных действий или встраивания в строки текста.

---

## 1. Когда использовать

- Для **вторичных** или **тернарных** действий, которые не требуют визуального выделения.
    
- В диалогах: “Отмена” рядом с основной “Подтвердить”.
    
- В строках списка или карточках, где кнопка должна сливаться с фоном.
    

---

## 2. Базовый синтаксис

```kotlin
@Composable
fun SimpleTextButton() {
    TextButton(onClick = { /* действие */ }) {
        Text("TextButton")
    }
}
```

Параметры:

- `onClick: () -> Unit` — вызывается при нажатии.
    
- `modifier: Modifier` — отступы, размеры, выравнивание.
    
- `enabled: Boolean` — по умолчанию `true`; при `false` кнопка “приглушается” (меняется цвет текста).
    

---

## 3. Стандартный внешний вид

- **Фон**: прозрачный.
    
- **Цвет текста**: `colorScheme.primary` (или `contentColor` из темы).
    
- **Отступы**: `ButtonDefaults.ContentPadding` (обычно небольшие, чтобы текст не прилипал к краю).
    
- **Нет тени и рамки**.
    

---

## 4. Кастомизация

### 4.1. Цвета

```kotlin
val myColors = ButtonDefaults.textButtonColors(
    contentColor = Color(0xFF6200EE),
    disabledContentColor = Color.Gray
)

TextButton(
    onClick = { /*...*/ },
    colors = myColors
) {
    Text("Custom Text Color")
}
```

### 4.2. Форма и рамка

Хотя у `TextButton` нет рамки по умолчанию, вы можете добавить её через `border` и изменить форму:

```kotlin
TextButton(
    onClick = {},
    shape = RoundedCornerShape(16.dp),
    border = BorderStroke(1.dp, MaterialTheme.colorScheme.primary)
) {
    Text("With Border")
}
```

### 4.3. Отступы (`contentPadding`)

```kotlin
TextButton(
    onClick = {},
    contentPadding = PaddingValues(horizontal = 8.dp, vertical = 4.dp)
) {
    Text("Tight Padding")
}
```

---

## 5. Иконка и текст

```kotlin
TextButton(onClick = { /*...*/ }) {
    Icon(
        imageVector = Icons.Default.FavoriteBorder,
        contentDescription = "Favorite",
        modifier = Modifier.size(18.dp)
    )
    Spacer(Modifier.width(4.dp))
    Text("Like")
}
```

---

## 6. Интеграция с темой

Чтобы все `TextButton` в приложении наследовали нужные вам цвета, настройте тему:

```kotlin
@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    val colors = lightColorScheme(
        primary = Color(0xFF1E88E5),
        onPrimary = Color.White,
        // … другие цвета
    )

    MaterialTheme(
        colorScheme = colors,
        typography = Typography(),
        content = content
    )
}
```

---

## 7. Полный пример

```kotlin
@Composable
fun TextButtonDemo() {
    Column(verticalArrangement = Arrangement.spacedBy(12.dp)) {
        // 1. Простая TextButton
        TextButton(onClick = {}) {
            Text("Default TextButton")
        }

        // 2. Отключённая
        TextButton(onClick = {}, enabled = false) {
            Text("Disabled TextButton")
        }

        // 3. Кастомный цвет текста
        TextButton(
            onClick = {},
            colors = ButtonDefaults.textButtonColors(contentColor = Color.Magenta)
        ) {
            Text("Magenta Text")
        }

        // 4. С рамкой и скруглениями
        TextButton(
            onClick = {},
            shape = RoundedCornerShape(8.dp),
            border = BorderStroke(1.dp, MaterialTheme.colorScheme.secondary)
        ) {
            Text("Outlined Text")
        }

        // 5. Иконка + текст
        TextButton(onClick = {}) {
            Icon(Icons.Default.Info, contentDescription = null)
            Spacer(Modifier.width(4.dp))
            Text("Info")
        }
    }
}
```

---

## 8. Лучшие практики

- **Не делайте** все кнопки `TextButton` — сохраняйте иерархию важности.
    
- **Используйте** для “мягких” действий, где важно минимальное пространство и ненавязчивость.
    
- **Проверяйте** `enabled=false`: текст должен потерять контраст, показывая пользователю, что действие недоступно.
    
- **Комбинируйте** с иконками, когда нужен компактный, но информативный элемент управления.