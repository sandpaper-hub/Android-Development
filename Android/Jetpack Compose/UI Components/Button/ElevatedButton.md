**`ElevatedButton`** — это один из вариантов кнопок в библиотеке Material 3 для Jetpack Compose. Он похож на базовую `Button`, но визуально «поднят» над поверхностью за счёт тени, что подчёркивает его важность.

## Когда использовать

- Чтобы обозначить действия с высоким приоритетом на фоне менее важных элементов.
    
- Когда нужно создать «акцентную» кнопку, но не слишком яркую, как `FilledTonalButton`.
    
- Для случаев, когда дизайн предполагает тень под кнопкой.

## Базовый синтаксис
```kotlin
@Composable
fun ElevatedButtonExample() {
    ElevatedButton(
        onClick = { /* Обработка клика */ }
    ) {
        Text("Elevated")
    }
}
```

Параметры конструктора:

- **onClick:** `() -> Unit` — обязательный, вызывается при нажатии.
    
- **modifier:** `Modifier` — для размеров, отступов, выравнивания и т. п.
    
- **enabled:** `Boolean` — по умолчанию `true`. Если `false`, кнопка визуально «приглушена» и не реагирует.
    
- **shape:** `Shape` — форма кнопки (скруглённая, срезанная и т. д.).
    
- **colors:** `ButtonColors` — цвета фона и контента.
    
- **elevation:** `ButtonElevation` — настройки тени (высота по умолчанию, при нажатии, в отключённом состоянии).
    
- **contentPadding:** `PaddingValues` — внутренние отступы вокруг контента.

## Тень и `elevation`

По умолчанию `ElevatedButton` имеет следующие уровни приподнятости (в `dp`):

|Состояние|Высота тени (defaultElevation)|
|---|---|
|В обычном состоянии|1 dp|
|При нажатии|3 dp|
|Отключена (disabled)|0 dp|

Эти значения можно переопределить:
```kotlin
val customElevation = ButtonDefaults.elevatedButtonElevation(
    defaultElevation = 4.dp,
    pressedElevation = 8.dp,
    disabledElevation = 0.dp
)

ElevatedButton(
    onClick = {},
    elevation = customElevation
) {
    Text("Кастомная тень")
}
```

## Форма (`shape`)

Форма влияет на скругление углов и может быть любой, реализующей интерфейс `Shape`:
```kotlin
ElevatedButton(
    onClick = {},
    shape = RoundedCornerShape(24.dp)
) {
    Text("Round corners")
}
```

## Цвета (`colors`)

По умолчанию `ElevatedButton` берёт из темы:

- `containerColor` = `colorScheme.primary`
    
- `contentColor` = `colorScheme.onPrimary`
    

Для кастомизации:
```kotlin
val myColors = ButtonDefaults.elevatedButtonColors(
    containerColor = Color(0xFF6200EE),
    contentColor = Color.White,
    disabledContainerColor = Color(0xFFCCCCCC),
    disabledContentColor = Color(0xFF888888)
)

ElevatedButton(
    onClick = {},
    colors = myColors
) {
    Text("Кастомный цвет")
}
```

## Внутренние отступы (`contentPadding`)

Управляйте пространством вокруг текста или иконок:
```kotlin
ElevatedButton(
    onClick = {},
    contentPadding = PaddingValues(horizontal = 32.dp, vertical = 12.dp)
) {
    Text("Широкая кнопка")
}
```

## Комбинация с иконками
```kotlin
ElevatedButton(onClick = {}) {
    Icon(
        imageVector = Icons.Default.Check,
        contentDescription = "Галочка",
        modifier = Modifier.size(18.dp)
    )
    Spacer(Modifier.width(8.dp))
    Text("Подтвердить")
}
```

## ## Интеграция с темой

Чтобы все `ElevatedButton` в приложении имели единый стиль, настройте тему:

```kotlin
@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    val colors = lightColorScheme(
        primary = Color(0xFF0050EF),
        onPrimary = Color.White,
        // остальные цвета…
    )

    MaterialTheme(
        colorScheme = colors,
        typography = Typography(),
        content = content
    )
}
```

## Советы и лучшие практики

- **Используйте** `ElevatedButton` для ключевых действий, когда нужно визуально выделить кнопку на плоском фоне.
    
- **Не злоупотребляйте** тенью — слишком много «поднятых» кнопок создаёт загромождённый интерфейс.
    
- **Проверяйте** доступность: `enabled = false` → понятный «отключённый» вид.
    
- **Следите** за читаемостью текста при изменении контейнера.
