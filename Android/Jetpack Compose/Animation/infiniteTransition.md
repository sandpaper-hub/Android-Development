В **Jetpack Compose** `rememberInfiniteTransition` (часто говорят просто _infiniteTransition_) — это специальный инструмент для создания **бесконечных анимаций**, которые крутятся, пока ваш `Composable` находится в UI-дереве.

Если `animate*AsState` или `Transition` запускаются только при смене `targetValue`, то `infiniteTransition` сам по себе постоянно анимирует значения туда-обратно или заново.

---

## 🔹 Как работает

- Создаётся через `rememberInfiniteTransition()`.
    
- Возвращает объект `InfiniteTransition`.
    
- У него есть методы:
    
    - **`animateFloat()`**
        
    - **`animateColor()`**
        
    - **`animateValue()`** (для кастомных типов через `TwoWayConverter`).
        
- Каждая анимация указывает `initialValue`, `targetValue` и `animationSpec` (обычно `infiniteRepeatable`).
    

---

## 🔹 Простой пример: пульсация

```kotlin
@Composable
fun PulseExample() {
    val infinite = rememberInfiniteTransition(label = "pulse")

    val alpha by infinite.animateFloat(
        initialValue = 0.3f,
        targetValue = 1f,
        animationSpec = infiniteRepeatable(
            animation = tween(1000, easing = LinearEasing),
            repeatMode = RepeatMode.Reverse
        ),
        label = "alpha"
    )

    Box(
        modifier = Modifier
            .size(100.dp)
            .graphicsLayer(alpha = alpha)
            .background(Color.Magenta)
    )
}
```

🔹 Здесь квадрат постоянно "дышит" — прозрачность меняется от `0.3` до `1` и обратно.

---

## 🔹 Пример с цветом

```kotlin
@Composable
fun ColorLoopExample() {
    val infinite = rememberInfiniteTransition(label = "colorLoop")

    val color by infinite.animateColor(
        initialValue = Color.Red,
        targetValue = Color.Blue,
        animationSpec = infiniteRepeatable(
            animation = tween(1200),
            repeatMode = RepeatMode.Reverse
        ),
        label = "color"
    )

    Box(
        modifier = Modifier
            .size(120.dp)
            .background(color)
    )
}
```

🔹 Цвет бесконечно плавно переливается между красным и синим.

---

## 🔹 Когда использовать

- **Эффекты загрузки** (мигающий индикатор, "бегущие" точки).
    
- **Пульсирующие элементы** (кнопка «подтвердить», индикатор записи).
    
- **Фоновые анимации** (градиенты, движение волн, сияние).
    
- **Бесконечные циклы**, когда не нужно ждать события изменения состояния.
    

---

## 🔹 Важно помнить

- `infiniteTransition` работает только пока Composable на экране.
    
- Он **не зависит** от состояния, а сам генерирует значения бесконечно.
    
- Для одноразовых анимаций или анимаций «по событию» лучше использовать `animate*AsState` или `Animatable`.

