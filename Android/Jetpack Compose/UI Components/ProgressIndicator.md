В Jetpack Compose два основных индикатора загрузки:

1. **`CircularProgressIndicator`** — круговой индикатор (обычно для бесконечной загрузки).
    
2. **`LinearProgressIndicator`** — линейный индикатор (часто в верхней части экрана, как загрузка страницы).
    

---

## 🌀 CircularProgressIndicator

### 📌 Назначение:

Показывает бесконечную или конечную (по прогрессу) анимацию в виде круга.

### ✅ Простой пример (индикация бесконечной загрузки):

```kotlin
@Composable
fun LoadingCircle() {
    CircularProgressIndicator()
}
```

Это крутящийся круг по умолчанию.

---

### 📈 Пример с **заданным прогрессом**:

```kotlin
@Composable
fun DeterminateCircularProgress() {
    val progress = 0.65f // 65%
    CircularProgressIndicator(progress = progress)
}
```

- `progress` — значение от `0.0f` до `1.0f` (например, `0.5f` = 50%).
    

---

### 🎨 Кастомизация:

```kotlin
CircularProgressIndicator(
    progress = 0.4f,
    modifier = Modifier.size(48.dp),
    strokeWidth = 6.dp,
    color = Color.Green,
    trackColor = Color.LightGray
)
```

|Параметр|Описание|
|---|---|
|`progress`|Прогресс от 0.0 до 1.0|
|`modifier`|Размер и позиционирование|
|`strokeWidth`|Толщина линии|
|`color`|Цвет индикатора|
|`trackColor`|Цвет фона круга|

---

## 📏 LinearProgressIndicator

### 📌 Назначение:

Показывает загрузку в виде полоски. Может быть:

- **бесконечным** (анимированная полоса, как при загрузке сайта),
    
- **определённым** (заполненная часть от 0 до 100%).
    

---

### ✅ Пример бесконечной загрузки:

```kotlin
@Composable
fun LoadingLine() {
    LinearProgressIndicator()
}
```

---

### 📈 Пример с прогрессом:

```kotlin
@Composable
fun DeterminateLinearProgress() {
    val progress = 0.3f
    LinearProgressIndicator(progress = progress)
}
```

---

### 🎨 Кастомизация:

```kotlin
LinearProgressIndicator(
    progress = 0.75f,
    modifier = Modifier
        .fillMaxWidth()
        .height(8.dp),
    color = Color.Blue,
    trackColor = Color.LightGray
)
```

|Параметр|Описание|
|---|---|
|`progress`|Прогресс от 0.0 до 1.0|
|`modifier`|Ширина, высота и позиционирование|
|`color`|Цвет прогресс-бара|
|`trackColor`|Цвет фона под полоской|

---

## ⚙️ Пример с `LaunchedEffect` и прогрессом

```kotlin
@Composable
fun AnimatedProgressBar() {
    var progress by remember { mutableStateOf(0f) }

    LaunchedEffect(Unit) {
        while (progress < 1f) {
            delay(50)
            progress += 0.01f
        }
    }

    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        CircularProgressIndicator(progress = progress)
        Spacer(modifier = Modifier.height(16.dp))
        LinearProgressIndicator(progress = progress)
    }
}
```

---

## ✅ Когда использовать:

| Сценарий                                         | Компонент                                                                     |
| ------------------------------------------------ | ----------------------------------------------------------------------------- |
| Неизвестная длительность                         | `CircularProgressIndicator()` или `LinearProgressIndicator()` без параметров  |
| Определённый прогресс (например, загрузка файла) | `CircularProgressIndicator(progress)` или `LinearProgressIndicator(progress)` |
| В теле экрана                                    | `CircularProgressIndicator` по центру                                         |
| В верхней части или под кнопкой                  | `LinearProgressIndicator`                                                     |
