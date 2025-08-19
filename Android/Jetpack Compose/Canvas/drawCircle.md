## 🔹 Что делает `drawCircle`

Рисует **окружность** (или диск) на `Canvas`.  
Можно управлять:

- цветом или градиентом,
    
- радиусом,
    
- центром,
    
- прозрачностью,
    
- стилем (заливка или обводка).
    

---

## 🔹 Сигнатуры

Есть две основные версии:

```kotlin
fun DrawScope.drawCircle(
    color: Color,
    radius: Float = size.minDimension / 2,
    center: Offset = this.center,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

и

```kotlin
fun DrawScope.drawCircle(
    brush: Brush,
    radius: Float = size.minDimension / 2,
    center: Offset = this.center,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

👉 Первая версия — с обычным цветом (`Color.Red`),  
👉 Вторая — с кистью (`Brush`), например градиент.

---

## 🔹 Основные параметры

1. **color / brush**
    
    - Цвет круга или градиентная заливка.
        
2. **radius**
    
    - Радиус окружности (в пикселях).
        
    - По умолчанию = половина минимального измерения `Canvas`.
        
3. **center (Offset)**
    
    - Координаты центра круга.
        
    - По умолчанию = центр `Canvas`.
        
4. **alpha**
    
    - Прозрачность (от `0f` до `1f`).
        
5. **style (DrawStyle)**
    
    - `Fill` (по умолчанию, заливка).
        
    - `Stroke(width = …)` — рисует только обводку.
        
6. **colorFilter, blendMode**
    
    - Для сложных графических эффектов и наложений.
        

---

## 🔹 Примеры

### 1. Простой круг

```kotlin
@Composable
fun SimpleCircle() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawCircle(
            color = Color.Red,
            radius = 80f,
            center = center
        )
    }
}
```

👉 Красный круг радиусом 80 px в центре `Canvas`.

---

### 2. Круг с обводкой

```kotlin
@Composable
fun StrokedCircle() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawCircle(
            color = Color.Blue,
            radius = 70f,
            style = Stroke(width = 10f)
        )
    }
}
```

👉 Синий круг только с рамкой.

---

### 3. Круг с градиентом

```kotlin
@Composable
fun GradientCircle() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawCircle(
            brush = Brush.radialGradient(
                colors = listOf(Color.Magenta, Color.Cyan),
                center = center,
                radius = 100f
            ),
            radius = 100f
        )
    }
}
```

👉 Радиальный градиент от розового к голубому.

---

### 4. Полупрозрачный круг (оверлей)

```kotlin
@Composable
fun TransparentCircle() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawCircle(
            color = Color.Black,
            radius = 90f,
            alpha = 0.3f
        )
    }
}
```

👉 Используется для эффектов "подсветки" или затемнения.

---

### 5. Несколько кругов (аналог ripple)

```kotlin
@Composable
fun MultiCircle() {
    Canvas(modifier = Modifier.size(250.dp)) {
        for (i in 1..5) {
            drawCircle(
                color = Color.Green.copy(alpha = 0.2f * i),
                radius = 30f * i,
                center = center,
                style = Stroke(width = 5f)
            )
        }
    }
}
```

👉 Нарисует концентрические окружности (как ripple-анимация).

---

## 🔹 Где применяется

- Индикаторы (**точки**, пульсация).
    
- Кастомные **прогресс-бары** (кольцевые, сегменты).
    
- Графики (точки на линии).
    
- Эффекты (волнушки, ripple).
    
- Игры (шарики, кружочки).
    

---

📌 Итог:

- `drawCircle` = **универсальный инструмент** для рисования кругов/дисков.
    
- Он даёт больше контроля, чем `Box(shape = CircleShape)`: можно сделать обводку, градиент, полупрозрачность, несколько кругов в одной области.

