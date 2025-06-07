**`FilledTonalButton`** — это вариант кнопки из библиотеки Material 3 в Jetpack Compose. Визуально она похожа на `Button`, но использует «тональный» (тонко-выраженный) цветовой акцент, который позволяет создать вторичный, но всё же заметный элемент управления.

## 1. Когда использовать

- Когда вам нужен **вторичный** (secondary) вариант кнопки, который при этом остаётся заметным (в отличие от `TextButton` или `OutlinedButton`).
    
- Для действий среднего приоритета: они важнее простых ссылок, но уступают по значимости `Button` (filled primary).
    
- Когда дизайн предполагает использование цветовой палитры Material 3 с тонкими акцентами.

## 2. Базовый синтаксис
```kotlin
@Composable
fun FilledTonalExample() {
    FilledTonalButton(onClick = { /* действие */ }) {
        Text("Tonal")
    }
}
```

Параметры конструктора:

- **onClick**: `() -> Unit` — вызывается при нажатии.
    
- **modifier**: `Modifier` — для отступов, размеров, выравнивания.
    
- **enabled**: `Boolean` — `true` по умолчанию; `false` переводит кнопку в «отключённый» режим.
    
- **shape**: `Shape` — форма кнопки (скруглённые, срезанные углы и пр.).
    
- **colors**: `ButtonColors` — управление цветами контейнера и содержимого.
    
- **elevation**: `ButtonElevation` — настройка высоты «тона» (тональной тени).
    
- **contentPadding**: `PaddingValues` — внутренние отступы.

## 3. Цвета (`colors`)

По умолчанию `FilledTonalButton` берёт из темы:

- `containerColor` = `colorScheme.secondaryContainer`
    
- `contentColor` = `colorScheme.onSecondaryContainer`
    
- `disabledContainerColor` = `colorScheme.onSurface.copy(alpha = 0.12f)`
    
- `disabledContentColor` = `colorScheme.onSurface.copy(alpha = 0.38f)`
    

**Пример кастомных цветов:**
```kotlin
val tonalColors = ButtonDefaults.filledTonalButtonColors(
    containerColor = Color(0xFFBB86FC),
    contentColor = Color.Black,
    disabledContainerColor = Color.LightGray,
    disabledContentColor = Color.DarkGray
)

FilledTonalButton(
    onClick = { /*...*/ },
    colors = tonalColors
) {
    Text("Кастомный Tonal")
}
```

## 4. Тональная тень (`elevation` / `tonalElevation`)

В отличие от `ElevatedButton`, у `FilledTonalButton` используется `tonalElevation` — лёгкий объём, подчеркивающий тональный слой:
```kotlin
val tonalElevation = ButtonDefaults.filledTonalButtonElevation(
    defaultElevation = 1.dp,
    pressedElevation = 3.dp,
    disabledElevation = 0.dp
)

FilledTonalButton(
    onClick = { /*...*/ },
    elevation = tonalElevation
) {
    Text("Тональная тень")
}
```

- **defaultElevation** — высота в обычном состоянии.
    
- **pressedElevation** — при нажатии.
    
- **disabledElevation** — когда `enabled = false`.
## 5. Форма (`shape`)

Можно задать любую форму:
```kotlin
FilledTonalButton(
    onClick = {},
    shape = RoundedCornerShape(topStart = 0.dp, bottomEnd = 16.dp)
) {
    Text("Свой shape")
}
```

## 6. Внутренние отступы (`contentPadding`)

Управляют пространством вокруг текста/иконок:
```kotlin
FilledTonalButton(
    onClick = {},
    contentPadding = PaddingValues(horizontal = 24.dp, vertical = 10.dp)
) {
    Text("Широкая тональная")
}
```

## 7. Иконки и текст
```kotlin
FilledTonalButton(onClick = {}) {
    Icon(
        imageVector = Icons.Default.Info,
        contentDescription = null,
        modifier = Modifier.size(18.dp)
    )
    Spacer(Modifier.width(8.dp))
    Text("Подробнее")
}
```

## 8. Интеграция с темой

Чтобы все `FilledTonalButton` в приложении автоматически наследовали нужные вам цвета и формы, настройте `MaterialTheme`:
```kotlin
@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    val colors = lightColorScheme(
        primary = Color(0xFF0061A4),
        secondaryContainer = Color(0xFFCEE5FF),
        onSecondaryContainer = Color(0xFF001D36),
        // ... другие цвета
    )

    MaterialTheme(
        colorScheme = colors,
        typography = Typography(),
        content = content
    )
}

```