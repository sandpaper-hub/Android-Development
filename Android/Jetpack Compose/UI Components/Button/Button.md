
### Что такое Button
`Button` — это базовый компонент для отображения кликабельной кнопки на экране. Он содержит слот `content`, в который обычно помещают текст, иконку или их комбинацию. По умолчанию стили кнопки берутся из текущей темы Material 3.

```kotlin
@Composable
fun SimpleButton() {
    Button(onClick = { /* действие */ }) {
        Text("Нажми меня")
    }
}
```

- **onClick**: обязательный лямбда-параметр, вызывается при клике.
    
- **enabled** (по умолчанию `true`): флаг, отключающий взаимодействие и меняющий внешний вид.

### Варианты кнопок в Material 3
Помимо базового `Button`, в Material 3 есть несколько вариантов:

| Компонент                                | Описание                               |
| ---------------------------------------- | -------------------------------------- |
| `Button`                                 | Базовая «filled» кнопка                |
| [ElevatedButton](<ElevatedButton>)       | Кнопка с тенью (поднятая над фоном)    |
| [FilledTonalButton](<FilledTonalButton>) | Тональная (secondary) кнопка           |
| [OutlinedButton](<OutlinedButton>)       | Кнопка с контуром, без заливки         |
| `TextButton`                             | «Текстовая» кнопка, минимальный акцент |
| `IconButton`                             | Кнопка только с иконкой                |
### Настройка внешнего вида
#### Цвета и состояние

Через параметр `colors: ButtonColors` можно задать свои цвета:
```kotlin
val customColors = ButtonDefaults.buttonColors(
    containerColor = Color.Magenta,
    contentColor = Color.White,
    disabledContainerColor = Color.Gray,
    disabledContentColor = Color.LightGray
)

Button(
    onClick = {},
    colors = customColors
) {
    Text("Кастомный цвет")
}
```

#### Форма и тень
- **shape**: `RoundedCornerShape`, `CutCornerShape` и т.п.
    
- **elevation**: только для `ElevatedButton`: тень вокруг кнопки.
    
- **tonalElevation**: для `FilledTonalButton`.
```kotlin
Button(
    onClick = {},
    shape = RoundedCornerShape(16.dp),
    elevation = ButtonDefaults.elevatedButtonElevation(defaultElevation = 8.dp)
) {
    Text("С закруглёнными углами")
}
```

#### Отступы контента
Параметр `contentPadding` контролирует внутренние отступы:
```kotlin
Button(
    onClick = {},
    contentPadding = PaddingValues(horizontal = 32.dp, vertical = 12.dp)
) {
    Text("Широкая кнопка")
}
```

### Работа с иконками и текстом
Обычно используют комбинацию `Icon` + `Text`:
```kotlin
Button(onClick = {}) {
    Icon(
        imageVector = Icons.Default.ArrowForward,
        contentDescription = null,
        modifier = Modifier.size(18.dp)
    )
    Spacer(Modifier.width(8.dp))
    Text("Далее")
}
```
Для этого можно также вынести `Button` в переиспользуемый компонент:
```kotlin
@Composable
fun IconTextButton(
    icon: ImageVector,
    text: String,
    onClick: () -> Unit
) {
    Button(onClick = onClick) {
        Icon(icon, contentDescription = null)
        Spacer(Modifier.width(4.dp))
        Text(text)
    }
}
```

### Интеграция с темой
Кнопки автоматически подхватывают палитру из `MaterialTheme.colorScheme`. Чтобы изменить глобально, можно переопределить `buttonColors` в теме:
```kotlin
@Composable
fun MyTheme(content: @Composable () -> Unit) {
    val colors = lightColorScheme(
        primary = Color(0xFF6200EE),
        onPrimary = Color.White,
        // ...
    )
    MaterialTheme(
        colorScheme = colors,
        typography = Typography(),
        content = content
    )
}

```