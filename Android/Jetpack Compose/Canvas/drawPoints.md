`drawPoints` — это функция `DrawScope`, которая позволяет **нарисовать сразу много точек или линий** за один вызов. Она менее популярна, чем `drawCircle` или `drawPath`, но полезна для графиков, scatter-plot, сеток и сложных рисунков.

---

## 🔹 Сигнатура

```kotlin
fun DrawScope.drawPoints(
    points: List<Offset>,
    pointMode: PointMode,
    color: Color,
    strokeWidth: Float = Stroke.HairlineWidth,
    cap: StrokeCap = StrokeCap.Butt,
    pathEffect: PathEffect? = null,
    alpha: Float = 1.0f,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DefaultBlendMode
)
```

---

## 🔹 Основные параметры

1. **points**
    
    - Список координат (`Offset(x, y)`) — набор точек.
        
2. **pointMode (PointMode)**  
    Определяет, как будут интерпретироваться точки:
    
    - `PointMode.Points` → каждая точка отдельно.
        
    - `PointMode.Lines` → рисуются **линии между парами точек**.
        
    - `PointMode.Polygon` → рисуется **последовательный многоугольник** (линии соединяют все точки по порядку).
        
3. **color**  
    Цвет точек/линий.
    
4. **strokeWidth**  
    Толщина (работает для линий и для размера точки).
    
5. **cap (StrokeCap)**  
    Вид конца точки/линии:
    
    - `Butt` (обрезанный),
        
    - `Round` (округлый),
        
    - `Square` (квадратный).
        
6. **pathEffect**  
    Можно сделать пунктир, волнистую линию и т.п.
    
7. **alpha**  
    Прозрачность.
    

---

## 🔹 Примеры

### 1. Набор отдельных точек

```kotlin
@Composable
fun PointsExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawPoints(
            points = listOf(
                Offset(20f, 20f),
                Offset(100f, 50f),
                Offset(160f, 120f)
            ),
            pointMode = PointMode.Points,
            color = Color.Red,
            strokeWidth = 15f,
            cap = StrokeCap.Round
        )
    }
}
```

👉 Нарисует три красные точки (кружки).

---

### 2. Линии между парами точек

```kotlin
@Composable
fun LinesExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawPoints(
            points = listOf(
                Offset(20f, 20f),
                Offset(180f, 20f), // первая линия
                Offset(20f, 100f),
                Offset(180f, 100f) // вторая линия
            ),
            pointMode = PointMode.Lines,
            color = Color.Blue,
            strokeWidth = 8f,
            cap = StrokeCap.Round
        )
    }
}
```

👉 Нарисует две горизонтальные синие линии.

---

### 3. Полигон (соединяет все точки)

```kotlin
@Composable
fun PolygonExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawPoints(
            points = listOf(
                Offset(100f, 20f),  // верх
                Offset(180f, 180f), // справа внизу
                Offset(20f, 180f),  // слева внизу
                Offset(100f, 20f)   // возврат к началу
            ),
            pointMode = PointMode.Polygon,
            color = Color.Green,
            strokeWidth = 6f
        )
    }
}
```

👉 Нарисует зелёный треугольник (замкнутый).

---

## 🔹 Где применяется

- Scatter-plot (**диаграммы точками**).
    
- Построение сетки или маркеров.
    
- Соединение точек для графика.
    
- Быстрая отрисовка многоугольников.
    
- Минималистичные фигуры (точечные индикаторы, grid).
    

---

📌 Итог:

- `drawCircle` → один круг.
    
- `drawLine` → одна линия.
    
- `drawPoints` → много точек/линий/многоугольников **за один вызов**.
