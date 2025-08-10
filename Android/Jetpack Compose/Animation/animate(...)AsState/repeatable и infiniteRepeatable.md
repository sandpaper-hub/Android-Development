В **Jetpack Compose** `repeatable` — это **специальный тип `AnimationSpec`**, который повторяет анимацию заданное количество раз.  
Он относится к семейству анимационных спецификаций вроде `tween`, `spring`, `keyframes` и используется, например, в `animate*AsState`, `updateTransition` или `Animatable`.

---

## 1. Сигнатура

```kotlin
fun <T> repeatable(
    iterations: Int,
    animation: DurationBasedAnimationSpec<T>,
    repeatMode: RepeatMode = RepeatMode.Restart,
    initialStartOffset: StartOffset = StartOffset(0)
): RepeatableSpec<T>
```

---

## 2. Параметры

|Параметр|Описание|
|---|---|
|**`iterations`**|Сколько раз повторить анимацию|
|**`animation`**|Базовая анимация (обычно `tween` или `keyframes`)|
|**`repeatMode`**|`Restart` (начинать заново) или `Reverse` (играть в обратную сторону)|
|**`initialStartOffset`**|Задержка перед запуском и между циклами|

---

## 3. Пример

```kotlin
@Composable
fun RepeatableAutoStart() {
    var target by remember { mutableStateOf(100.dp) }
    val size by animateDpAsState(
        targetValue = target,
        animationSpec = repeatable(
            iterations = 5,
            animation = tween(500),
            repeatMode = RepeatMode.Reverse
        ),
        label = "size"
    )

    LaunchedEffect(Unit) {
        target = 200.dp // меняем targetValue -> анимация запустится
    }

    Box(Modifier.size(size).background(Color.Red))
}
```

🔹 Здесь квадрат будет плавно увеличиваться до 200.dp и уменьшаться обратно **5 раз подряд**.

---

## 4. `RepeatMode`

- **`Restart`** — каждый цикл начинается с исходного значения (A → B, A → B, A → B).
    
- **`Reverse`** — циклы чередуют направление (A → B, B → A, A → B).
    

---

## 5. Отличие от `infiniteRepeatable`

- `repeatable` — фиксированное число повторов (`iterations`).
    
- `infiniteRepeatable` — бесконечный повтор.
    

Пример бесконечной версии:

```kotlin
val alpha by infiniteRepeatable(
    animation = tween(500),
    repeatMode = RepeatMode.Reverse
)
```
