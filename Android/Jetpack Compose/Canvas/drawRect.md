## 🔹 Что делает drawRect

Рисует **прямоугольник** на `Canvas`.  
В отличие от `Box`, здесь мы сами управляем:

- размером,
    
- позицией,
    
- цветом,
    
- заливкой или обводкой,
    
- прозрачностью,
    
- эффектами.
    

---

## 🔹 Сигнатуры

Есть две перегрузки:

```kotlin
fun DrawScope.drawRect(
    color: Color,
    topLeft: Offset = Offset.Zero,
    size: Size = this.size,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

и

```kotlin
fun DrawScope.drawRect(
    brush: Brush,
    topLeft: Offset = Offset.Zero,
    size: Size = this.size,
    alpha: Float = 1.0f,
    style: DrawStyle = Fill,
    colorFilter: ColorFilter? = null,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

👉 Первая версия — с обычным цветом (`Color.Red`),  
👉 Вторая — с **кистью (`Brush`)**, например градиент.

---

## 🔹 Основные параметры

1. **color / brush**
    
    - Задаёт цвет (`Color.Blue`) или градиент (`Brush.linearGradient(...)`).
        
2. **topLeft (Offset)**
    
    - Координата верхнего левого угла.
        
    - По умолчанию `(0f, 0f)` — в левом верхнем углу `Canvas`.
        
3. **size (Size)**
    
    - Размер прямоугольника (`Size(width, height)`).
        
    - По умолчанию равен всему `Canvas`.
        
4. **alpha**
    
    - Прозрачность (0f = полностью прозрачный, 1f = полностью видимый).
        
5. **style (DrawStyle)**
    
    - `Fill` (по умолчанию, заливка).
        
    - `Stroke(width = ...)` — рисует только обводку.
        
6. **colorFilter, blendMode**
    
    - Для эффектов смешивания цветов (обычно используются в более сложных случаях).
        

---

## 🔹 Примеры

### 1. Прямоугольник заливкой

```kotlin
@Composable
fun FilledRect() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawRect(
            color = Color.Red,
            topLeft = Offset(20f, 20f),
            size = Size(100f, 60f)
        )
    }
}
```

👉 Нарисует красный прямоугольник 100×60 px со смещением 20 px от верхнего левого угла.

---

### 2. Прямоугольник только с обводкой

```kotlin
@Composable
fun StrokedRect() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawRect(
            color = Color.Blue,
            topLeft = Offset(40f, 40f),
            size = Size(120f, 80f),
            style = Stroke(width = 8f)
        )
    }
}
```

👉 Синий прямоугольник без заливки, только рамка толщиной 8 px.

---

### 3. Прямоугольник с градиентом

```kotlin
@Composable
fun GradientRect() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawRect(
            brush = Brush.linearGradient(
                colors = listOf(Color.Magenta, Color.Cyan)
            ),
            topLeft = Offset(0f, 0f),
            size = Size(size.width, size.height / 2)
        )
    }
}
```

👉 Прямоугольник с линейным градиентом (половина Canvas).

---

### 4. Полупрозрачный прямоугольник (оверлей)

```kotlin
@Composable
fun OverlayRect() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawRect(
            color = Color.Black,
            alpha = 0.3f // прозрачность
        )
    }
}
```

👉 Можно использовать как затемнение/оверлей.

---

## 🔹 Где применяется

- Фоны, оверлеи, выделение областей.
    
- Сетка (grid) — прямоугольники в цикле.
    
- Прогресс-бары (заполненный прямоугольник, который растёт).
    
- Диаграммы (бар-чарты).
    
- Игры (тайлы, блоки, коллизии).
    

---

📌 Итог:

- `Box(background = …)` = простой прямоугольник.
    
- `drawRect` = **много прямоугольников, кастомные размеры, обводка, прозрачность, градиенты, циклы, динамическое рисование**.
