`Switch` в Jetpack Compose — это компонент-переключатель, который позволяет пользователю включать и выключать какое-либо состояние (например, "вкл/выкл уведомления").

---

### 🔌 Базовое использование

```kotlin
var isChecked by remember { mutableStateOf(false) }

Switch(
    checked = isChecked,
    onCheckedChange = { isChecked = it }
)
```

---

### 📋 Параметры

|Параметр|Тип|Описание|
|---|---|---|
|`checked`|`Boolean`|Текущее состояние переключателя|
|`onCheckedChange`|`(Boolean) -> Unit`|Обработчик изменения состояния|
|`enabled`|`Boolean`|Можно ли взаимодействовать с `Switch`|
|`colors`|`SwitchColors`|Кастомизация цветов|
|`modifier`|`Modifier`|Модификаторы: размер, отступы и т.д.|

---

### 📘 Пример с текстом

```kotlin
@Composable
fun DarkModeToggle() {
    var isDarkMode by remember { mutableStateOf(false) }

    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier.padding(16.dp)
    ) {
        Text(text = if (isDarkMode) "Тёмная тема" else "Светлая тема")
        Spacer(modifier = Modifier.width(8.dp))
        Switch(
            checked = isDarkMode,
            onCheckedChange = { isDarkMode = it }
        )
    }
}
```

---

### 🎨 Кастомизация внешнего вида

```kotlin
Switch(
    checked = isChecked,
    onCheckedChange = { isChecked = it },
    colors = SwitchDefaults.colors(
        checkedThumbColor = Color.Green,
        uncheckedThumbColor = Color.Gray,
        checkedTrackColor = Color.LightGray
    )
)
```

---

### 🔒 Отключённое состояние

```kotlin
Switch(
    checked = true,
    onCheckedChange = {},
    enabled = false
)
```

---

### ⚙️ Использование с ViewModel

```kotlin
val isEnabled by viewModel.switchState.collectAsState()

Switch(
    checked = isEnabled,
    onCheckedChange = { viewModel.setSwitchState(it) }
)
```
