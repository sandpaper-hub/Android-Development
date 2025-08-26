**`drawImage`** — это функция в `DrawScope`, которая позволяет рисовать растровые картинки (**`ImageBitmap`**) прямо внутри `Canvas`.

---

## 🔹 Сигнатуры

В Compose есть несколько перегрузок `drawImage`.

### 1. Простая версия

```kotlin
fun DrawScope.drawImage(
    image: ImageBitmap,
    topLeft: Offset = Offset.Zero,
    alpha: Float = 1.0f,
    colorFilter: ColorFilter? = null,
    style: DrawStyle = Fill,
    blendMode: BlendMode = DefaultBlendMode
)
```

👉 Просто рисует картинку начиная с координаты `topLeft`.

---

### 2. С выбором области (`src` и `dst`)

```kotlin
fun DrawScope.drawImage(
    image: ImageBitmap,
    srcOffset: IntOffset = IntOffset.Zero,
    srcSize: IntSize = IntSize(image.width, image.height),
    dstOffset: IntOffset = IntOffset.Zero,
    dstSize: IntSize = srcSize,
    alpha: Float = 1.0f,
    colorFilter: ColorFilter? = null,
    style: DrawStyle = Fill,
    blendMode: BlendMode = DefaultBlendMode
)
```

👉 Здесь можно выбрать, **какой участок картинки** взять (`srcOffset`, `srcSize`) и **куда поместить** (`dstOffset`, `dstSize`).  
Это нужно для:

- обрезки изображения,
    
- масштабирования,
    
- спрайтовых анимаций.
    

---

## 🔹 Основные параметры

- **image** — картинка (`ImageBitmap`). Можно получить из ресурсов:
    
    ```kotlin
    val image = ImageBitmap.imageResource(R.drawable.my_icon)
    ```
    
- **topLeft / dstOffset** — куда рисовать (верхний левый угол).
    
- **srcOffset / srcSize** — какую часть картинки брать (например, кусочек спрайта).
    
- **dstSize** — в каком размере выводить (можно растянуть/уменьшить).
    
- **alpha** — прозрачность (от `0f` до `1f`).
    
- **colorFilter** — позволяет наложить тонировку (например, закрасить в другой цвет).
    
- **blendMode** — режим смешивания с фоном.
    

---

## 🔹 Примеры

### 1. Нарисовать картинку

```kotlin
@Composable
fun SimpleImage() {
    val image = ImageBitmap.imageResource(R.drawable.ic_launcher_foreground)

    Canvas(modifier = Modifier.size(200.dp)) {
        drawImage(
            image = image,
            topLeft = Offset(40f, 60f)
        )
    }
}
```

👉 Нарисует иконку со смещением.

---

### 2. Масштабировать под Canvas

```kotlin
@Composable
fun ScaledImage() {
    val image = ImageBitmap.imageResource(R.drawable.ic_launcher_background)

    Canvas(modifier = Modifier.size(250.dp)) {
        drawImage(
            image = image,
            dstSize = IntSize(size.width.toInt(), size.height.toInt())
        )
    }
}
```

👉 Растянет картинку на весь Canvas.

---

### 3. Вырезать часть картинки (спрайт)

```kotlin
@Composable
fun SpriteExample() {
    val spriteSheet = ImageBitmap.imageResource(R.drawable.spritesheet)

    Canvas(modifier = Modifier.size(200.dp)) {
        drawImage(
            image = spriteSheet,
            srcOffset = IntOffset(0, 0),
            srcSize = IntSize(spriteSheet.width / 4, spriteSheet.height),
            dstSize = IntSize(100, 100),
            dstOffset = IntOffset(50, 50)
        )
    }
}
```

👉 Нарисует только первую колонку из спрайтового листа, в масштабе 100×100.

---

## 🔹 Где применяется

- Отображение картинок внутри `Canvas`.
    
- Игры: отрисовка **спрайтов**, **тайлов**.
    
- Кастомные UI: например, **фоновая картинка + поверх фигуры**.
    
- Постобработка: цветовые фильтры, прозрачность.
    
- Анимация: смена кадров из `spritesheet`.
    

---

📌 Итог:

- `drawImage` = рисуем `ImageBitmap` в Canvas.
    
- Можно просто отобразить картинку, растянуть её или вырезать кусок.
    
- Очень полезно для **кастомных виджетов и игр**.