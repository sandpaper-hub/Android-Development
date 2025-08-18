`Modifier.animateEnterExit()` в Jetpack Compose — это **модификатор**, который позволяет задавать **анимации появления и исчезновения для конкретного элемента**, если он находится внутри контейнера с анимацией (например, `AnimatedVisibility`).

То есть вместо того, чтобы анимировать **весь блок целиком**, ты можешь настроить отдельные анимации для **каждого дочернего элемента**.

---

## 🔹 Сигнатура

```kotlin
fun Modifier.animateEnterExit(
    enter: EnterTransition = EnterTransition.None,
    exit: ExitTransition = ExitTransition.None
): Modifier
```

- **`enter`** — анимация появления (`fadeIn`, `slideIn*`, `expandIn`, `scaleIn` и т. д.).
    
- **`exit`** — анимация исчезновения (`fadeOut`, `slideOut*`, `shrinkOut`, `scaleOut` и т. д.).
    

---

## 🔹 Пример использования

### Анимация отдельных элементов внутри `AnimatedVisibility`

```kotlin
@Composable
fun AnimateEnterExitExample() {
    var visible by remember { mutableStateOf(true) }

    Column(Modifier.padding(16.dp)) {
        Button(onClick = { visible = !visible }) {
            Text("Toggle")
        }

        AnimatedVisibility(visible = visible) {
            Column {
                Text(
                    "Fade + Slide",
                    modifier = Modifier.animateEnterExit(
                        enter = fadeIn() + slideInHorizontally(),
                        exit = fadeOut() + slideOutHorizontally()
                    )
                )
                Text(
                    "Scale",
                    modifier = Modifier.animateEnterExit(
                        enter = scaleIn(),
                        exit = scaleOut()
                    )
                )
            }
        }
    }
}
```

🔹 Здесь:

- Первый `Text` будет **въезжать слева + появляться** и **выезжать вправо + исчезать**.
    
- Второй `Text` будет **увеличиваться при входе** и **уменьшаться при выходе**.
    

---

## 🔹 Отличие от `AnimatedVisibility`

- **`AnimatedVisibility`** управляет входом/выходом **всего блока**.
    
- **`animateEnterExit()`** позволяет задать анимацию **для отдельных дочерних элементов** внутри блока.
    

Комбинация этих двух подходов даёт гибкость:  
можно сделать так, чтобы контейнер появлялся с одной анимацией, а его содержимое — с другой.

---
## Ошибки с `AnimatedVisibility` + `animateEnterExit`

1. **По умолчанию контейнер `AnimatedVisibility` тоже анимируется** (быстрый `fadeIn/fadeOut`).
    
2. **Дети внутри ещё раз анимируются** через `animateEnterExit`.
    
3. Если щёлкать во время анимации (а у тебя 2000 мс!), получается «переназначение цели» посреди перехода и «двойные» анимации конфликтуют. Визуально это выглядит как «нужно подождать, чтобы снова сработало корректно».
    

Ниже два надёжных паттерна

---

### Вариант A — отключить анимацию контейнера и дебаунсить клик

```kotlin
@Composable
fun AnimateEnterExitFixed() {
    // 1) Управляем видимостью через состояние перехода, чтобы знать, когда анимация занята
    val visibleState = remember { MutableTransitionState(false) }
    val isAnimating = !visibleState.isIdle   // true, пока идёт enter/exit

    Column(Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(12.dp)) {

        // 2) Блокируем кнопку, пока идёт анимация (или можно игнорировать клик)
        Button(
            enabled = !isAnimating,
            onClick = {
                visibleState.targetState = !visibleState.currentState
            }
        ) {
            Text(if (visibleState.targetState) "Скрыть" else "Показать")
        }

        // 3) ВАЖНО: вырубаем анимации на контейнере, чтобы не было «двойных» переходов
        AnimatedVisibility(
            visibleState = visibleState,
            enter = EnterTransition.None,
            exit = ExitTransition.None,
            label = "container"
        ) {
            Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
                Text(
                    "Fade + Slide",
                    modifier = Modifier
                        .fillMaxWidth()
                        .animateEnterExit(
                            enter = fadeIn(animationSpec = tween(1200)) +
                                    slideInHorizontally(
                                        initialOffsetX = { -it },
                                        animationSpec = tween(1200)
                                    ),
                            exit = fadeOut(animationSpec = tween(1200)) +
                                   slideOutHorizontally(
                                       targetOffsetX = { it },
                                       animationSpec = tween(1200)
                                   )
                        )
                )
                Text(
                    "Scale",
                    modifier = Modifier
                        .fillMaxWidth()
                        .animateEnterExit(
                            enter = scaleIn(animationSpec = tween(1200, easing = LinearOutSlowInEasing)) + fadeIn(),
                            exit  = scaleOut(animationSpec = tween(1200)) + fadeOut()
                        )
                )
            }
        }
    }
}
```

- **Убрали** анимации у контейнера (`EnterTransition.None/ExitTransition.None`), оставили их **только на детях**.
    
- **Заблокировали кнопку**, пока переход не завершён (`!visibleState.isIdle`).
    
- Тайминги симметричны для `enter`/`exit`, чтобы не было «дёрганья».
    

---

### Вариант B — не блокировать клик, а «очередь» переключений

Если хочется принимать клики во время анимации и воспроизводить их **после** завершения текущей:

```kotlin
@Composable
fun AnimateEnterExitQueued() {
    val visibleState = remember { MutableTransitionState(false) }
    var pendingToggle by remember { mutableStateOf(false) }

    LaunchedEffect(visibleState.isIdle) {
        if (visibleState.isIdle && pendingToggle) {
            pendingToggle = false
            visibleState.targetState = !visibleState.currentState
        }
    }

    Column(Modifier.padding(16.dp)) {
        Button(onClick = {
            if (visibleState.isIdle) {
                visibleState.targetState = !visibleState.currentState
            } else {
                pendingToggle = true   // отложим до конца текущей анимации
            }
        }) { Text(if (visibleState.targetState) "Скрыть" else "Показать") }

        AnimatedVisibility(
            visibleState = visibleState,
            enter = EnterTransition.None,
            exit = ExitTransition.None
        ) {
            // ... дети с animateEnterExit, как в варианте A
        }
    }
}
```

---

### Ещё 3 коротких совета

- **Сократи длительность**: 2000 мс — это очень долго, шанс «поймать» переключение посреди анимации высокий. 500–900 мс обычно достаточно.
    
- **Делай одинаковые спецификации** для `enter` и `exit` одного элемента (симметрия = меньше артефактов при инвалидации).
    
- **Не дублируй анимации**: либо контейнер (`AnimatedVisibility` с `enter/exit`), либо дети (`animateEnterExit`), но не всё сразу — иначе эффекты накладываются.
## 🔹 Где полезно

- Списки (например, элементы появляются с разным сдвигом/задержкой).
    
- Сложные карточки (контейнер «выезжает», а кнопки внутри «проявляются»).
    
- Поэтапное появление UI (staggered animation).