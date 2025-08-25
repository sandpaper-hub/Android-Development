## 🔹 Что делает drawText

`drawText` — это способ нарисовать текст прямо внутри **Canvas**, с поддержкой:

- `TextStyle` (цвет, размер, шрифт, вес и т.д.),
    
- переносов строк (`softWrap`),
    
- обрезки (`TextOverflow`),
    
- ограничения по строкам (`maxLines`),
    
- управления областью (`size`).
    

👉 Фактически это **"Text внутри Canvas"**.

---

## 🔹 Сигнатура

```kotlin
fun DrawScope.drawText(
    textMeasurer: TextMeasurer,
    text: String,
    topLeft: Offset = Offset.Zero,
    style: TextStyle = TextStyle.Default,
    overflow: TextOverflow = TextOverflow.Clip,
    softWrap: Boolean = true,
    maxLines: Int = Int.MAX_VALUE,
    size: Size = Size.Unspecified,
    blendMode: BlendMode = DrawScope.DefaultBlendMode
)
```

---

## 🔹 Основные параметры

1. **textMeasurer**
    
    - Обязательный объект, который отвечает за измерение текста.
        
    - Создаётся так:
        
        ```kotlin
        val textMeasurer = rememberTextMeasurer()
        ```
        
2. **text**
    
    - Строка для отрисовки.
        
3. **topLeft (Offset)**
    
    - Координата верхнего левого угла текста.
        
4. **style (TextStyle)**
    
    - Стиль текста: цвет, шрифт, размер, вес, тени и т.д.
        
    - Такой же, как у `Text`.
        
5. **overflow (TextOverflow)**
    
    - Что делать, если текст не влезает: `Clip`, `Ellipsis`, `Visible`.
        
6. **softWrap**
    
    - Разрешать перенос строк (`true` по умолчанию).
        
7. **maxLines**
    
    - Ограничение по количеству строк.
        
8. **size (Size)**
    
    - Область, в которую текст должен уложиться (по умолчанию без ограничений).
        
9. **blendMode**
    
    - Как смешивать с фоном (обычно не трогаем).
        

---

## 🔹 Примеры

### 1. Простой текст

```kotlin
@Composable
fun SimpleTextOnCanvas() {
    val textMeasurer = rememberTextMeasurer()

    Canvas(modifier = Modifier.size(250.dp)) {
        drawText(
            textMeasurer = textMeasurer,
            text = "Hello Canvas!",
            topLeft = Offset(40f, 100f),
            style = TextStyle(
                color = Color.Red,
                fontSize = 24.sp,
                fontWeight = FontWeight.Bold
            )
        )
    }
}
```

👉 Нарисует красный жирный текст на Canvas.

---

### 2. Многострочный текст с переносами

```kotlin
@Composable
fun MultiLineTextOnCanvas() {
    val textMeasurer = rememberTextMeasurer()

    Canvas(modifier = Modifier.size(250.dp)) {
        drawText(
            textMeasurer = textMeasurer,
            text = "Jetpack Compose Canvas позволяет рисовать и текст тоже!",
            topLeft = Offset(10f, 20f),
            style = TextStyle(color = Color.Blue, fontSize = 16.sp),
            size = Size(width = size.width - 20f, height = size.height),
            softWrap = true,
            maxLines = 3,
            overflow = TextOverflow.Ellipsis
        )
    }
}
```

👉 Если текст длиннее 3 строк, он обрежется с `...`.

---

### 3. Текст с разными стилями (AnnotatedString)

```kotlin
@Composable
fun StyledTextOnCanvas() {
    val textMeasurer = rememberTextMeasurer()

    Canvas(modifier = Modifier.size(300.dp)) {
        drawText(
            textMeasurer = textMeasurer,
            text = buildAnnotatedString {
                withStyle(SpanStyle(color = Color.Magenta, fontSize = 22.sp)) {
                    append("Hello, ")
                }
                withStyle(SpanStyle(color = Color.Green, fontWeight = FontWeight.Bold)) {
                    append("Compose!")
                }
            },
            topLeft = Offset(20f, 60f)
        )
    }
}
```

👉 Можно комбинировать стили внутри одной надписи.

---

## 🔹 Где применять

- **Подписи к фигурам** (`drawArc`, `drawCircle`, `drawRect`).
    
- **Диаграммы и графики** (ось X/Y, проценты, метки).
    
- **Игры и визуализации** (счёт, название уровня).
    
- **Инфографика** (подписи к элементам).
    
- **Кастомные виджеты** (например, индикатор с числовым значением внутри).