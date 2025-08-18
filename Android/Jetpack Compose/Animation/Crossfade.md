В **Jetpack Compose** `Crossfade` — это готовый `@Composable`, который позволяет плавно переключаться между **двумя состояниями UI** с помощью анимации прозрачности (_fade_).

Он работает как упрощённая версия `AnimatedContent`: когда меняется состояние, старое содержимое плавно исчезает, а новое появляется.

---

## 🔹 Сигнатура

```kotlin
@Composable
fun <T> Crossfade(
    targetState: T,
    animationSpec: FiniteAnimationSpec<Float> = tween(),
    label: String = "Crossfade",
    contentKey: (targetState: T) -> Any? = { it },
    content: @Composable (targetState: T) -> Unit
)
```

### Параметры

- **`targetState`** — текущее состояние, определяющее, что рисовать.
    
- **`animationSpec`** — настройка анимации (по умолчанию `tween`).
    
- **`contentKey`** — ключ, определяющий, когда обновлять контент (обычно можно оставить как есть).
    
- **`content`** — лямбда с UI для каждого состояния.
    

---

## 🔹 Пример 1 — Переключение цветов

```kotlin
@Composable
fun CrossfadeExample() {
    var toggled by remember { mutableStateOf(false) }

    Column(Modifier.padding(16.dp)) {
        Button(onClick = { toggled = !toggled }) {
            Text("Toggle")
        }

        Crossfade(targetState = toggled, animationSpec = tween(1000)) { state ->
            Box(
                modifier = Modifier
                    .size(150.dp)
                    .background(if (state) Color.Green else Color.Magenta)
            )
        }
    }
}
```

🔹 Здесь `Box` плавно меняет цвет между зелёным и розовым за 1 секунду.

---

## 🔹 Пример 2 — Переключение экранов

```kotlin
enum class Screen { HOME, SETTINGS }

@Composable
fun CrossfadeNavigation() {
    var screen by remember { mutableStateOf(Screen.HOME) }

    Column {
        Row {
            Button(onClick = { screen = Screen.HOME }) { Text("Home") }
            Button(onClick = { screen = Screen.SETTINGS }) { Text("Settings") }
        }

        Crossfade(targetState = screen) { current ->
            when (current) {
                Screen.HOME -> Box(
                    Modifier.size(200.dp).background(Color.Cyan),
                    contentAlignment = Alignment.Center
                ) { Text("Home Screen") }

                Screen.SETTINGS -> Box(
                    Modifier.size(200.dp).background(Color.Yellow),
                    contentAlignment = Alignment.Center
                ) { Text("Settings Screen") }
            }
        }
    }
}
```

🔹 Отлично подходит для **простых переключений экранов/контента** без сложных анимаций.

---

## 🔹 Когда использовать `Crossfade`

✅ Если нужно быстро и просто переключать содержимое с плавной анимацией.  
✅ Для смены UI-состояния (иконки, кнопки, вкладки).  
✅ Для лёгкой навигации между небольшим количеством экранов.

❌ Не подходит для сложных переходов (там лучше `AnimatedContent` или `Transition`).