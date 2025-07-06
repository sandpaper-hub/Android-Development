`AlertDialog` в Jetpack Compose — это всплывающее модальное окно, которое требует от пользователя подтверждения или выбора. Часто используется для предупреждений, подтверждений, ошибок и т.д.

---

### 📌 Базовый пример

```kotlin
fun AlertDialogExample() {  
    var showDialog by remember { mutableStateOf(false) }  
  
    Button(onClick = { showDialog = true }) {  
        Text("Show dialog")  
    }  
  
    if (showDialog){  
        AlertDialog(  
            onDismissRequest = { showDialog = false},  
            confirmButton = {  
                TextButton(onClick = {showDialog = false}) {  
                    Text("Delete")  
                }  
            },  
            dismissButton = {  
                TextButton(onClick = {showDialog = false}) {  
                    Text("Cancel")  
                }  
            },  
            title = {  
                Text("Are you sure?")  
            },  
            text = {  
                Text("This action can't cancel")  
            }  
        )  
    }
}
```

---

### 🔧 Параметры `AlertDialog`

|Параметр|Тип|Описание|
|---|---|---|
|`onDismissRequest`|`() -> Unit`|Вызывается при нажатии вне окна или при нажатии "назад"|
|`title`|`@Composable (() -> Unit)?`|Заголовок диалога|
|`text`|`@Composable (() -> Unit)?`|Основной текст|
|`confirmButton`|`@Composable () -> Unit`|Кнопка подтверждения|
|`dismissButton`|`@Composable (() -> Unit)?`|Кнопка отмены (необязательно)|
|`modifier`|`Modifier`|Модификаторы для размера, отступов и т.д.|
|`shape`, `containerColor`, `icon`|Дополнительная кастомизация внешнего вида||

---

### 🎨 Пример с иконкой и кастомными цветами

```kotlin
AlertDialog(
    onDismissRequest = { showDialog = false },
    icon = { Icon(Icons.Default.Warning, contentDescription = null) },
    title = { Text("Ошибка") },
    text = { Text("Что-то пошло не так.") },
    confirmButton = {
        TextButton(onClick = { showDialog = false }) {
            Text("Ок", color = Color.Red)
        }
    },
    containerColor = Color(0xFFFFF3F3),
    shape = RoundedCornerShape(16.dp)
)
```

---

### 📲 Как вызвать AlertDialog

```kotlin
Button(onClick = { showDialog = true }) {
    Text("Показать диалог")
}
```

---

### ✅ Применение в ViewModel-подходе

```kotlin
val showDialog by viewModel.showDialog.collectAsState()

if (showDialog) {
    AlertDialog(
        ...
        confirmButton = {
            TextButton(onClick = {
                viewModel.onConfirm()
            }) {
                Text("Да")
            }
        },
        dismissButton = {
            TextButton(onClick = {
                viewModel.onDismiss()
            }) {
                Text("Нет")
            }
        }
    )
}
```
