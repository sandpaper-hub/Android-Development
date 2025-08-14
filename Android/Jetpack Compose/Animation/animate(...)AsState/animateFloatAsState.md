`animateFloatAsState` в **Jetpack Compose** — это `@Composable`-функция, которая анимирует одно значение типа `Float` и возвращает его в виде `State<Float>`.  
Она используется, когда нужно плавно изменять любые параметры, которые принимают `Float`: прозрачность, поворот, масштаб, прогресс и т. д.

---

## 1. Что делает

- Принимает **`targetValue: Float`** — конечное значение, к которому нужно прийти.
    
- При изменении этого значения запускает плавный переход от текущего значения к новому.
    
- Не требует вручную запускать или останавливать анимацию.
    
- Работает в декларативном стиле: анимация стартует при смене `targetValue`.
    

---

## 2. Сигнатура

```kotlin
@Composable
fun animateFloatAsState(
    targetValue: Float,
    animationSpec: AnimationSpec<Float> = spring(),
    label: String = "FloatAnimation",
    finishedListener: ((Float) -> Unit)? = null
): State<Float>
```

**Параметры:**

- **`targetValue`** — значение, к которому анимируем.
    
- **`animationSpec`** — тип анимации (`tween`, `spring`, `keyframes`, `repeatable`, `infiniteRepeatable`).
    
- **`label`** — имя для отладки.
    
- **`finishedListener`** — коллбэк, который вызовется по окончании анимации.
    

---

## 3. Пример — прозрачность

```kotlin
@Composable
fun FadeInOutExample() {
    var visible by remember { mutableStateOf(true) }
    val alpha by animateFloatAsState(
        targetValue = if (visible) 1f else 0f,
        animationSpec = tween(durationMillis = 1000),
        label = "alpha"
    )

    Box(
        modifier = Modifier
            .size(150.dp)
            .graphicsLayer(alpha = alpha)
            .background(Color.Magenta)
            .clickable { visible = !visible }
    )
}
```

🔹 При клике `alpha` плавно изменяется между `1f` (полностью видно) и `0f` (прозрачный).

---

## 4. Пример — вращение

```kotlin
@Composable
fun RotateExample() {
    var rotated by remember { mutableStateOf(false) }
    val rotation by animateFloatAsState(
        targetValue = if (rotated) 360f else 0f,
        animationSpec = tween(durationMillis = 800, easing = LinearEasing),
        label = "rotation"
    )

    Box(
        modifier = Modifier
            .size(100.dp)
            .graphicsLayer(rotationZ = rotation)
            .background(Color.Cyan)
            .clickable { rotated = !rotated }
    )
}
```

🔹 Можно анимировать любое свойство, которое принимает `Float`.

---

## 5. Где применяется

- **Прозрачность (`alpha`)**
    
- **Повороты (`rotationX`, `rotationY`, `rotationZ`)**
    
- **Масштабирование (`scaleX`, `scaleY`)**
    
- **Прогресс индикаторов**
    
- **Смещения в `offset` или `translation`**
    
- **Кастомные вычисления на основе float-параметров** (например, радиус окружности в Canvas)

