`Slider` в Jetpack Compose — это компонент пользовательского интерфейса, позволяющий пользователю выбрать значение из непрерывного диапазона, двигая ползунок.

---

### 📌 Базовый синтаксис

```kotlin
var sliderPosition by remember { mutableStateOf(0f) }

Slider(
    value = sliderPosition,
    onValueChange = { sliderPosition = it },
    valueRange = 0f..100f,
    steps = 0 // Количество промежуточных делений между min и max
)
```

---

### 🔧 Основные параметры

|Параметр|Описание|
|---|---|
|`value`|Текущее значение ползунка (`Float`)|
|`onValueChange`|Лямбда, вызываемая при изменении значения|
|`valueRange`|Диапазон значений (`ClosedFloatingPointRange<Float>`)|
|`steps`|Количество делений между `start` и `end` (без крайних точек)|
|`onValueChangeFinished`|Колбэк, вызывается один раз после завершения перетаскивания|
|`modifier`|Модификаторы (`Modifier.fillMaxWidth()`, `padding()` и т.д.)|
|`enabled`|Можно ли взаимодействовать с `Slider`|

---

### 📘 Пример со значением и подписью

```kotlin
@Composable
fun VolumeSlider() {
    var volume by remember { mutableStateOf(50f) }

    Column(modifier = Modifier.padding(16.dp)) {
        Text(text = "Громкость: ${volume.toInt()}")
        Slider(
            value = volume,
            onValueChange = { volume = it },
            valueRange = 0f..100f
        )
    }
}
```

---

### ⚙️ Дискретные значения (с делениями)

```kotlin
Slider(
    value = selectedValue,
    onValueChange = { selectedValue = it },
    valueRange = 0f..10f,
    steps = 9 // 10 значений: 0, 1, 2, ..., 10
)
```

---

### 🎨 Кастомизация внешнего вида

Если хочешь изменить цвета:

```kotlin
Slider(
    value = volume,
    onValueChange = { volume = it },
    colors = SliderDefaults.colors(
        thumbColor = Color.Red,
        activeTrackColor = Color.Green,
        inactiveTrackColor = Color.Gray
    )
)
```

---

### 📍 Если нужен диапазон значений

Можно использовать `RangeSlider` (если нужен выбор **интервала**):

```kotlin
var range by remember { mutableStateOf(0f..100f) }

RangeSlider(
    value = range,
    onValueChange = { range = it },
    valueRange = 0f..100f
)
```
