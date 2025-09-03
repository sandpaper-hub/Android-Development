## 🔹 Canvas

`Canvas` — это Composable, внутри которого можно рисовать произвольную графику: линии, круги, текст, картинки и т.д.  
Он предоставляет пространство, где можно вручную описать, что и как будет нарисовано.

Пример:

```kotlin
@Composable
fun CanvasExample() {
    Canvas(modifier = Modifier.size(200.dp)) {
        drawCircle(
            color = Color.Red,
            radius = size.minDimension / 2,
            center = center
        )
    }
}
```

В этом примере:

- `Canvas` создаёт область размером 200×200 dp.
    
- Внутри блока используется `DrawScope` (об этом ниже).
    
- Метод `drawCircle` рисует красный круг.
    

---

## 🔹 DrawScope

`DrawScope` — это контекст, внутри которого происходит рисование. Он автоматически доступен внутри лямбды `Canvas`.

Что в нём есть:

1. **Размеры области**:
    
    - `size` — размер `Canvas` (ширина и высота).
        
    - `center` — центр области.
        
2. **Функции рисования**:
    
    - `drawRect()`, `drawCircle()`, `drawLine()`, `drawArc()`, `drawPath()`, `drawImage()`, `drawText()` (через вспомогательные API).
        
3. **Возможность трансформации**:
    
    - `rotate(degrees) { ... }`
        
    - `scale(scaleX, scaleY) { ... }`
        
    - `translate(left, top) { ... }`  
        Эти функции временно изменяют систему координат внутри блока.
        
4. **Работа с цветом и стилем**:
    
    - `Color`, `Brush` (градиенты, заливки), `Stroke` (линии).
        

Пример с несколькими фигурами:

```kotlin
@Composable
fun MultipleDrawExample() {
    Canvas(modifier = Modifier.size(250.dp)) {
        // Красный прямоугольник
        drawRect(Color.Red, size = size / 2f)

        // Синий круг в центре
        drawCircle(Color.Blue, radius = 60f, center = center)

        // Диагональная линия
        drawLine(
            color = Color.Green,
            start = Offset(0f, 0f),
            end = Offset(size.width, size.height),
            strokeWidth = 5f
        )
    }
}
```

---

## 🔹 Отличие Canvas от DrawScope

- `Canvas` — это **Composable-контейнер**, который задаёт место на экране для рисования.
    
- `DrawScope` — это **контекст рисования внутри Canvas**, который предоставляет API для отрисовки.
    

Можно сказать:  
👉 **Canvas = "лист бумаги"**,  
👉 **DrawScope = "набор кистей, красок и инструментов"**.

---

## 🔹 Где это используется

- Кастомные графики (диаграммы, графы, прогресс-бары).
    
- Игровая графика или анимации.
    
- Эффекты (тени, клиппинг, градиенты).
    
- Отрисовка UI-элементов, которых нет в стандартных компонентах Compose.

- [[drawRect]]
- [[drawCircle]]
- [[drawLine]]
- [[drawArc]]
- [[drawImage]]
- [[drawPath]]
- [[drawPoints]]
- [[drawText]]
- [[Градиент]]
- [[Трансформации]]