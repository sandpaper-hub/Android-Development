## 🧩 Что такое `DropdownMenu`?

Это выпадающее меню, которое появляется по нажатию кнопки или иконки. Оно не блокирует основной экран, но временно перекрывает часть интерфейса для выбора одного из пунктов.

---

## 🔨 Структура:

- `DropdownMenu`: контейнер, внутри которого находятся пункты меню.
    
- `DropdownMenuItem`: отдельный пункт меню.
    
- `expanded`: `Boolean`, определяет видимость меню.
    
- `onDismissRequest`: вызывается при клике вне меню или при нажатии "назад".
    
- `IconButton`: обычно используется для вызова меню.
    

---

## ✅ Полный пример: Контекстное меню с иконкой

```kotlin
@Composable
fun DropdownMenuSample() {
    var expanded by remember { mutableStateOf(false) }

    Box(modifier = Modifier.fillMaxWidth(), contentAlignment = Alignment.TopEnd) {
        IconButton(onClick = { expanded = true }) {
            Icon(Icons.Default.MoreVert, contentDescription = "Меню")
        }

        DropdownMenu(
            expanded = expanded,
            onDismissRequest = { expanded = false }
        ) {
            DropdownMenuItem(
                onClick = {
                    expanded = false
                    println("Выбрали Редактировать")
                },
                text = { Text("Редактировать") }
            )
            DropdownMenuItem(
                onClick = {
                    expanded = false
                    println("Выбрали Удалить")
                },
                text = { Text("Удалить") }
            )
        }
    }
}
```

---

## 📌 Разбор кода:

| Часть              | Назначение                                                |
| ------------------ | --------------------------------------------------------- |
| `expanded`         | Хранит состояние видимости меню                           |
| `IconButton`       | Кнопка с иконкой "три точки", по которой открывается меню |
| `onDismissRequest` | Закрывает меню при клике вне его                          |
| `DropdownMenuItem` | Элементы меню с действиями                                |

---

## 📎 Пример в `TopAppBar`:

Если хочешь встроить меню в `TopAppBar`, вот как это делается:

```kotlin
@Composable
fun TopBarWithMenu() {
    var expanded by remember { mutableStateOf(false) }

    TopAppBar(
        title = { Text("Моё приложение") },
        actions = {
            IconButton(onClick = { expanded = true }) {
                Icon(Icons.Default.MoreVert, contentDescription = "Меню")
            }

            DropdownMenu(
                expanded = expanded,
                onDismissRequest = { expanded = false }
            ) {
                DropdownMenuItem(onClick = {
                    expanded = false
                    // Например, показать Snackbar
                }, text = { Text("Настройки") })

                DropdownMenuItem(onClick = {
                    expanded = false
                }, text = { Text("О приложении") })
            }
        }
    )
}
```

---

## 🧠 Советы:

- Меню автоматически отображается поверх других компонентов, но может быть смещено с помощью `offset`.
    
- Используй `remember` для хранения состояния `expanded`.
    
- Меню не должно быть слишком длинным — не более 5–7 пунктов.

