В Jetpack Compose, если стандартных компонентов вроде `Column`, `Row`, `Box` недостаточно, ты можешь создать **кастомный контейнер компоновки (custom layout)** и даже **кастомный модификатор компоновки (custom layout modifier)**. Это мощные инструменты, дающие полный контроль над измерением (`measure`) и размещением (`place`) дочерних элементов.

---

## 🔧 1. Кастомный контейнер компоновки (`Layout` API)

Ты можешь создать свой layout, используя функцию `Layout` или расширение `Modifier.layout`.

### 📦 Пример кастомного layout-контейнера: "OverlappingLayout"

```kotlin
@Composable
fun OverlappingLayout(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->

        val placeables = measurables.map { it.measure(constraints) }

        val width = placeables.maxOfOrNull { it.width } ?: 0
        val height = placeables.maxOfOrNull { it.height } ?: 0

        layout(width, height) {
            // Располагаем все элементы поверх друг друга
            placeables.forEach {
                it.placeRelative(0, 0)
            }
        }
    }
}
```

📌 В этом примере все дочерние элементы будут отрисованы один поверх другого, начиная с позиции `(0, 0)`.

### Пример кастомного контейнера ~LinearLayout
```kotlin
Box(  
    Modifier.fillMaxWidth()  
        .height(400.dp)  
) {  
    MyLayout(spacing = 20) {  
        Box(Modifier.size(50.dp).background(Color.DarkGray))  
        Box(Modifier.size(50.dp).background(Color.Red))  
        Box(Modifier.size(50.dp).background(Color.Green))  
        Box(Modifier.size(50.dp).background(Color.Blue))  
        Box(Modifier.size(50.dp).background(Color.Yellow))  
    }  
}

@Composable  
@Suppress("FunctionName")  
fun MyLayout(modifier: Modifier = Modifier, spacing: Int = 0, content: @Composable () -> Unit) {  
    Layout(modifier = modifier, content = content) { measurables, constraints ->  
        val placeables = measurables.map { measurable ->  
            measurable.measure(constraints)  
        }  
        layout(constraints.maxWidth, constraints.maxHeight) {  
            var yCoordinate = 0  
            var xCoordinate = 0  
            placeables.forEach { placeable ->  
                placeable.placeRelative(x = xCoordinate, y = yCoordinate)  
                xCoordinate += placeable.width + spacing  
                if ((xCoordinate + placeable.width) > constraints.maxWidth) {  
                    yCoordinate += placeable.height + spacing  
                    xCoordinate = 0  
                }  
            }  
        }    
    }
}
```

---

## ⚙️ 2. Кастомный модификатор компоновки (`Modifier.layout`)

Ты можешь изменить поведение любого composable через модификатор `Modifier.layout`.

### 📐 Пример: модификатор, сдвигающий элемент вниз на половину его высоты

```kotlin
fun Modifier.shiftDownByHalf(): Modifier = this.then(
    Modifier.layout { measurable, constraints ->
        val placeable = measurable.measure(constraints)

        layout(placeable.width, placeable.height + placeable.height / 2) {
            placeable.placeRelative(0, placeable.height / 2)
        }
    }
)
```

📌 Теперь ты можешь применить этот модификатор к любому элементу:

```kotlin
Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Red)
        .shiftDownByHalf()
)
```

---

## ✍ Как работает кастомная компоновка?

Компонент `Layout(...)` требует от тебя:

1. **Измерить** дочерние `Measurable` с помощью `.measure(constraints)`
    
2. **Определить размеры** текущего контейнера через `layout(width, height)`
    
3. **Разместить** элементы с помощью `.place(...)` или `.placeRelative(...)`
    

---

## 🧩 Когда использовать что?

|Случай|Используй|
|---|---|
|Хочешь полностью контролировать размещение дочерних элементов|`Layout { measurables, constraints -> ... }`|
|Хочешь изменить **положение** или **размер** одного элемента|`Modifier.layout`|
|Хочешь изменить только **размер** элемента без влияния на размещение|`Modifier.onSizeChanged` или `Modifier.layoutId` в `ConstraintLayout`|
