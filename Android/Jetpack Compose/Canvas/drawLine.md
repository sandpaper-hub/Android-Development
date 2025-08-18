## 🔹 Что делает `drawLine`

Функция рисует **прямую линию** между двумя точками (`start` и `end`) в пределах `Canvas`.

Сигнатура:

```kotlin
fun DrawScope.drawLine(
    color: Color,
    start: Offset,
    end: Offset,
    strokeWidth: Float = Stroke.HairlineWidth,
    cap: StrokeCap = StrokeCap.Butt,
    pathEffect: PathEffect? = null,
    alpha: Float = 1.0f,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

---

## 🔹 Основные параметры

1. **color**  
    Цвет линии (`Color.Red`, `Color.Blue`, и т.д.).
    
2. **start / end (Offset)**
    
    - `Offset(x, y)` — координаты начала и конца линии.
        
    - Координаты задаются в **px**, а не в dp (так как Canvas работает в пикселях).
        
    - `Offset(0f, 0f)` = верхний левый угол Canvas.
        
3. **strokeWidth**  
    Толщина линии в пикселях (`Float`).  
    По умолчанию = `Stroke.HairlineWidth` (очень тонкая линия).
    
4. **cap (StrokeCap)**  
    Вид окончания линии:
    
    - `Butt` (обрезанное) — по умолчанию.
        
    - `Round` (скруглённое).
        
    - `Square` (как прямоугольный маркер).
        
5. **pathEffect**  
    Эффект линии (например, пунктир).  
    Можно использовать `PathEffect.dashPathEffect(floatArrayOf(20f, 10f))` для пунктира.
    
6. **alpha**  
    Прозрачность (от 0f до 1f).
    
7. **blendMode / colorFilter**  
    Используются для сложных графических эффектов (например, смешивания цветов).
    

---

## 🔹 Пример: простая линия

```kotlin
@Composable
fun SimpleLine() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawLine(
            color = Color.Red,
            start = Offset(0f, 0f),
            end = Offset(size.width, size.height),
            strokeWidth = 5f
        )
    }
}
```

👉 Нарисует красную диагональ из левого верхнего угла в правый нижний.

---

## 🔹 Пример: несколько линий с разными концами

```kotlin
@Composable
fun LineCapsExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        val y = size.height / 2

        drawLine(
            color = Color.Blue,
            start = Offset(20f, y - 40f),
            end = Offset(size.width - 20f, y - 40f),
            strokeWidth = 20f,
            cap = StrokeCap.Butt
        )

        drawLine(
            color = Color.Green,
            start = Offset(20f, y),
            end = Offset(size.width - 20f, y),
            strokeWidth = 20f,
            cap = StrokeCap.Round
        )

        drawLine(
            color = Color.Red,
            start = Offset(20f, y + 40f),
            end = Offset(size.width - 20f, y + 40f),
            strokeWidth = 20f,
            cap = StrokeCap.Square
        )
    }
}
```

👉 Будет три линии: синяя (обрезанная), зелёная (скруглённая), красная (с квадратными концами).

---

## 🔹 Пример: пунктир

```kotlin
@Composable
fun DashedLine() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawLine(
            color = Color.Magenta,
            start = Offset(0f, size.height / 2),
            end = Offset(size.width, size.height / 2),
            strokeWidth = 10f,
            pathEffect = PathEffect.dashPathEffect(floatArrayOf(30f, 15f), 0f)
        )
    }
}
```

👉 Нарисует пунктирную линию: 30px штрих, 15px пробел.

---

## 🔹 Где применяется

- Разделители (кастомные, например, пунктирные).
    
- Построение координатных сеток.
    
- Графики (линейные, сеточные).
    
- Визуальные эффекты (подсветки, траектории движения в играх).
