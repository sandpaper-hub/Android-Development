`Snackbar` — это компонент пользовательского интерфейса в Android, предназначенный для краткого отображения сообщений пользователю. Обычно используется для уведомлений о действиях или ошибках, с возможностью предоставить обратную связь (например, кнопку "Отмена").

---

## 🔧 Основное поведение

- **Появляется внизу экрана** (над навигационной панелью, если она есть).
    
- **Автоматически исчезает** через короткое время.
    
- Может содержать **текст и кнопку действия** (например, "UNDO", "RETRY").
    
- Используется для **ненавязчивого** взаимодействия — не блокирует пользователя.
    

---
# ✅ Сигнатура `Snackbar` в Compose

```kotlin
@Composable
fun Snackbar(
    modifier: Modifier = Modifier,
    action: (@Composable () -> Unit)? = null,
    dismissAction: (@Composable () -> Unit)? = null,
    shape: Shape = SnackbarDefaults.shape,
    containerColor: Color = SnackbarDefaults.color,
    contentColor: Color = contentColorFor(containerColor),
    actionContentColor: Color = SnackbarDefaults.actionContentColor,
    dismissActionContentColor: Color = SnackbarDefaults.dismissActionContentColor,
    tonalElevation: Dp = SnackbarDefaults.Elevation,
    shadowElevation: Dp = 0.dp,
    content: @Composable () -> Unit
)
```

| Параметр                    | Тип                         | Назначение                                                                     |
| --------------------------- | --------------------------- | ------------------------------------------------------------------------------ |
| `modifier`                  | `Modifier`                  | Настройки внешнего вида: отступы, фон, границы и т.д.                          |
| `action`                    | `(@Composable () -> Unit)?` | Компонент кнопки действия, например, "UNDO".                                   |
| `dismissAction`             | `(@Composable () -> Unit)?` | Дополнительная кнопка закрытия, например, крестик.                             |
| `shape`                     | `Shape`                     | Форма контейнера (скругления и т.д.). По умолчанию — `SnackbarDefaults.shape`. |
| `containerColor`            | `Color`                     | Цвет фона Snackbar'а.                                                          |
| `contentColor`              | `Color`                     | Цвет текста внутри.                                                            |
| `actionContentColor`        | `Color`                     | Цвет текста в `action`-кнопке.                                                 |
| `dismissActionContentColor` | `Color`                     | Цвет иконки или текста в `dismissAction`.                                      |
| `tonalElevation`            | `Dp`                        | Эффект "высоты" (поднятость) — как бы тень.                                    |
| `shadowElevation`           | `Dp`                        | Настройка обычной тени (вместо tonalElevation).                                |
| `content`                   | `@Composable () -> Unit`    | Основной текст Snackbar’а.                                                     |


---
## 🧱 Пример в Jetpack Compose

```kotlin
@Composable
fun MyScreen() {
    val snackbarHostState = remember { SnackbarHostState() }
    val scope = rememberCoroutineScope()

    Scaffold(
        snackbarHost = { SnackbarHost(snackbarHostState) }
    ) {
        Button(onClick = {
            scope.launch {
                snackbarHostState.showSnackbar(
                    message = "Сохранено успешно",
                    actionLabel = "ОТМЕНА"
                )
            }
        }) {
            Text("Показать Snackbar")
        }
    }
}
```

### Что здесь происходит:

- `SnackbarHostState` управляет показом.
    
- `SnackbarHost()` размещает сам Snackbar внутри `Scaffold`.
    
- `showSnackbar()` отображает Snackbar с текстом и дополнительной кнопкой действия.
    

---

## ⚙️ Опции `showSnackbar`

```kotlin
suspend fun SnackbarHostState.showSnackbar(
    message: String,
    actionLabel: String? = null,
    duration: SnackbarDuration = SnackbarDuration.Short
): SnackbarResult
```

- `message`: текст сообщения.
    
- `actionLabel`: надпись на кнопке (например, “UNDO”).
    
- `duration`: длительность (Short, Long, Indefinite).
    
- `SnackbarResult`: можно узнать, нажал ли пользователь на кнопку действия.
    

---
# SnackBarHostState
`SnackbarHostState` — это **класс-состояние**, управляющий отображением Snackbar'ов в Jetpack Compose. Он отвечает за очередь сообщений, их показ, скрытие и возврат результата действия пользователя (например, нажал ли он на кнопку "UNDO").

---

## 🔍 Где используется

Чаще всего — вместе с `Scaffold` и `SnackbarHost`:

```kotlin
val snackbarHostState = remember { SnackbarHostState() }

Scaffold(
    snackbarHost = { SnackbarHost(snackbarHostState) }
) { ... }
```

---

## 🎮 Основные функции

### 1. **`showSnackbar()`**

```kotlin
suspend fun showSnackbar(
    message: String,
    actionLabel: String? = null,
    duration: SnackbarDuration = SnackbarDuration.Short
): SnackbarResult
```

- **message** — текст уведомления.
    
- **actionLabel** — текст кнопки (опционально).
    
- **duration** — длительность показа (`Short`, `Long`, `Indefinite`).
    
- **SnackbarResult** — `ActionPerformed` или `Dismissed`.
    

#### Пример:

```kotlin
val scope = rememberCoroutineScope()
val snackbarHostState = remember { SnackbarHostState() }

Button(onClick = {
    scope.launch {
        val result = snackbarHostState.showSnackbar(
            message = "Удалено",
            actionLabel = "ОТМЕНА"
        )
        if (result == SnackbarResult.ActionPerformed) {
            // пользователь нажал ОТМЕНА
        }
    }
}) {
    Text("Показать Snackbar")
}
```

---

### 2. **Очередь сообщений**

`SnackbarHostState` **ставит сообщения в очередь**, если вызывается `showSnackbar()` несколько раз подряд. Они будут показаны по очереди.

---

## 🧠 Важно понимать

- `SnackbarHostState` должен быть **сохранён в `remember{}`**, чтобы не пересоздавался при каждом recomposition.
    
- Метод `showSnackbar()` — **suspend-функция**, значит, нужно вызывать его из `CoroutineScope` (обычно через `rememberCoroutineScope()`).
    
- `SnackbarHostState` работает **в паре** с `SnackbarHost`.
    

---

## 💡 Пример использования в Scaffold

```kotlin
@Composable
fun MyScreen() {
    val snackbarHostState = remember { SnackbarHostState() }
    val scope = rememberCoroutineScope()

    Scaffold(
        snackbarHost = { SnackbarHost(hostState = snackbarHostState) }
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Button(onClick = {
                scope.launch {
                    snackbarHostState.showSnackbar(
                        message = "Данные сохранены",
                        actionLabel = "ОТМЕНА",
                        duration = SnackbarDuration.Long
                    )
                }
            }) {
                Text("Показать Snackbar")
            }
        }
    }
}
```

---

## 🧠 Когда использовать

- ✅ Подтверждение действия (например, "Файл удалён").
    
- ✅ Предложение отмены ("UNDO").
    
- ❌ Не использовать для критических ошибок (для этого лучше диалоги).
    
- ❌ Не перегружать кнопками и длинным текстом.
    

---

## 🎯 Полезные советы

- Используй `SnackbarDuration.Indefinite`, если хочешь дождаться действия пользователя.
    
- Не показывай более одного Snackbar подряд — они ставятся в очередь.
    
- Избегай слишком длинных текстов — Snackbar должен быть кратким и понятным.
    

---

