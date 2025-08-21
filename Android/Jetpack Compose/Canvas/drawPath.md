`drawPath` — самая универсальная функция в **`DrawScope`**, потому что она позволяет рисовать **любые произвольные фигуры**, а не только круги, прямоугольники или дуги.

---

## 🔹 Что делает `drawPath`

Рисует **путь (`Path`)**, который может состоять из:

- линий,
    
- кривых (квадратичные, кубические Безье),
    
- дуг,
    
- замкнутых или открытых контуров.
    

👉 С помощью `drawPath` можно создать **звезду, сердце, волну, многоугольник, иконку — всё, что угодно**.

---

## 🔹 Сигнатура

```kotlin
fun DrawScope.drawPath(
    path: Path,
    color: Color,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DefaultBlendMode
)
```

И вторая версия — с **Brush** (градиент/паттерн вместо цвета):

```kotlin
fun DrawScope.drawPath(
    path: Path,
    brush: Brush,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DefaultBlendMode
)
```

---

## 🔹 Основные параметры

- **path** — объект `Path`, в котором хранится описание фигуры.
    
- **color / brush** — заливка цветом или градиентом.
    
- **alpha** — прозрачность.
    
- **style** — `Fill` (по умолчанию, заливка) или `Stroke(width = …)` (только обводка).
    
- **blendMode, colorFilter** — для сложных графических эффектов.
    

---

## 🔹 Path — как он работает

`Path` задаётся пошагово:

- `moveTo(x, y)` — переместиться в точку (начало пути).
    
- `lineTo(x, y)` — нарисовать линию до точки.
    
- `quadTo(x1, y1, x2, y2)` — квадратичная кривая Безье.
    
- `cubicTo(...)` — кубическая кривая Безье.
    
- `arcTo(rect, startAngle, sweepAngle, forceMoveTo)` — дуга.
    
- `close()` — замкнуть путь (соединить последнюю точку с первой).
    

---

## 🔹 Примеры

### 1. Треугольник

```kotlin
@Composable
fun TriangleExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        val path = Path().apply {
            moveTo(size.width / 2, 0f) // верхняя вершина
            lineTo(size.width, size.height) // правая нижняя
            lineTo(0f, size.height) // левая нижняя
            close() // соединяем с верхом
        }
        drawPath(path = path, color = Color.Red)
    }
}
```

👉 Получится красный равнобедренный треугольник.

---

### 2. Волнистая линия

```kotlin
@Composable
fun WaveExample() {
    Canvas(modifier = Modifier.size(300.dp, 150.dp)) {
        val path = Path().apply {
            moveTo(0f, size.height / 2)
            quadraticBezierTo(
                size.width / 4, 0f,
                size.width / 2, size.height / 2
            )
            quadraticBezierTo(
                3 * size.width / 4, size.height,
                size.width, size.height / 2
            )
        }
        drawPath(path = path, color = Color.Blue, style = Stroke(width = 5f))
    }
}
```

👉 Нарисует синусоиду.

---

### 3. Сердце ❤️

```kotlin
@Composable
fun HeartExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        val width = size.width
        val height = size.height

        val path = Path().apply {
            moveTo(width / 2, height * 0.75f)
            cubicTo(width * 1.2f, height * 0.35f, width * 0.8f, 0f, width / 2, height * 0.25f)
            cubicTo(width * 0.2f, 0f, -width * 0.2f, height * 0.35f, width / 2, height * 0.75f)
            close()
        }

        drawPath(path = path, brush = Brush.linearGradient(
            listOf(Color.Red, Color.Magenta)
        ))
    }
}
```

👉 Градиентное сердце.

---

### 4. Многоугольник (например, пятиугольник)

```kotlin
@Composable
fun PolygonExample(sides: Int = 5) {
    Canvas(modifier = Modifier.size(200.dp)) {
        val path = Path()
        val radius = size.minDimension / 2
        val angle = (2 * Math.PI / sides).toFloat()

        path.moveTo(
            x = (radius * cos(0.0)).toFloat() + center.x,
            y = (radius * sin(0.0)).toFloat() + center.y
        )
        for (i in 1 until sides) {
            path.lineTo(
                x = (radius * cos(angle * i)).toFloat() + center.x,
                y = (radius * sin(angle * i)).toFloat() + center.y
            )
        }
        path.close()

        drawPath(path, color = Color.Green, style = Stroke(width = 5f))
    }
}
```

👉 Нарисует правильный многоугольник.

---

## 🔹 Где применяется

- Кастомные фигуры (сердца, звёзды, стрелки).
    
- Графики (линии, заполненные области).
    
- Волны, кривые, нестандартные UI.
    
- Маски, обрезка контента.
    
- Иконки прямо внутри `Canvas`.
    

---

📌 Итог:

- `drawLine`, `drawCircle`, `drawRect`, `drawArc` — специализированные функции.
    
- `drawPath` — **универсал**, с его помощью можно построить любую фигуру.