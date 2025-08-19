## 🔹 Сигнатура

```kotlin
fun DrawScope.drawArc(
    color: Color,
    startAngle: Float,
    sweepAngle: Float,
    useCenter: Boolean,
    topLeft: Offset = Offset.Zero,
    size: Size = this.size,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

Аналогично есть версия с **Brush** (для градиента).

---

## 🔹 Основные параметры

1. **color / brush**
    
    - Цвет заливки или кисть (градиент).
        
2. **startAngle**
    
    - Угол, откуда начинается дуга (в градусах).
        
    - Отсчёт идёт **по часовой стрелке** от **ось X вправо** (0° = вправо, 90° = вниз, 180° = влево, 270° = вверх).
        
3. **sweepAngle**
    
    - Насколько градусов рисовать (например, `90f` = четверть круга, `360f` = полный круг).
        
4. **useCenter**
    
    - `true` → рисует сектор (замыкается с центром).
        
    - `false` → рисует только дугу (кольцо).
        
5. **topLeft / size**
    
    - Задают прямоугольник, в который вписан круг/дуга.
        
    - По умолчанию `size` = весь `Canvas`.
        
6. **alpha**
    
    - Прозрачность.
        
7. **style**
    
    - `Fill` (по умолчанию — заливка).
        
    - `Stroke(width = …)` — только обводка.
        

---

## 🔹 Примеры

### 1. Четверть круга (сектор)

```kotlin
@Composable
fun ArcSector() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawArc(
            color = Color.Red,
            startAngle = 0f,
            sweepAngle = 90f,
            useCenter = true
        )
    }
}
```

👉 Нарисует красный сектор (четверть пиццы 🍕).

---

### 2. Просто дуга (без центра)

```kotlin
@Composable
fun ArcStroke() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawArc(
            color = Color.Blue,
            startAngle = -90f, // сверху
            sweepAngle = 180f, // полукруг
            useCenter = false,
            style = Stroke(width = 12f)
        )
    }
}
```

👉 Синий полукруг сверху, как прогресс-бар.

---

### 3. Кольцевой прогресс-бар

```kotlin
@Composable
fun CircularProgress(progress: Float) {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawArc(
            color = Color.Green,
            startAngle = -90f,
            sweepAngle = 360f * progress, // % от круга
            useCenter = false,
            style = Stroke(width = 20f, cap = StrokeCap.Round)
        )
    }
}
```

👉 Если `progress = 0.75f`, нарисует 75% круга (3/4).  
Очень похоже на `CircularProgressIndicator`, но кастомное.

---

### 4. Градиентная дуга

```kotlin
@Composable
fun GradientArc() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawArc(
            brush = Brush.sweepGradient(
                colors = listOf(Color.Magenta, Color.Cyan, Color.Yellow)
            ),
            startAngle = 0f,
            sweepAngle = 270f,
            useCenter = false,
            style = Stroke(width = 25f)
        )
    }
}
```

👉 Красиво для кастомных индикаторов.

---

## 🔹 Где применяется

- Прогресс-бары (круговые, полукруглые).
    
- Диаграммы (pie chart, donut chart).
    
- Индикаторы скорости, шагомеры.
    
- Игровые элементы (радар, прицел).
    
- Визуальные эффекты (сегменты, таймеры).
    

---

📌 Итог:

- `drawCircle` = полный круг.
    
- `drawArc` = часть круга (сектор или дуга).
    
- Вместе они позволяют рисовать **диаграммы, прогресс-бары, кольца, таймеры**.

