## 🔗 Что такое chain?

**Chain** — это когда **несколько элементов связаны друг с другом** по одной оси (start–end или top–bottom), и ты хочешь управлять:

- как они **выравниваются**
    
- как **распределяется пространство между ними**
    

---

## 🔨 Как создать цепочку в Jetpack Compose

Для этого используется метод `createHorizontalChain()` или `createVerticalChain()` внутри блока `ConstraintLayout`.

```kotlin
val (item1, item2, item3) = createRefs()

createHorizontalChain(item1, item2, item3, chainStyle = ChainStyle.Spread)
```

Ты вызываешь этот метод внутри `ConstraintLayout`, **после объявления ссылок**, и **до/после** применения `constrainAs`.

---

## 🧱 Полный пример с цепочкой

```kotlin
ConstraintLayout(modifier = Modifier.fillMaxSize()) {
    val (box1, box2, box3) = createRefs()

    // Создание цепочки
    createHorizontalChain(box1, box2, box3, chainStyle = ChainStyle.Spread)

    Box(
        modifier = Modifier
            .size(60.dp)
            .background(Color.Red)
            .constrainAs(box1) {
                top.linkTo(parent.top)
            }
    )

    Box(
        modifier = Modifier
            .size(60.dp)
            .background(Color.Green)
            .constrainAs(box2) {
                top.linkTo(parent.top)
            }
    )

    Box(
        modifier = Modifier
            .size(60.dp)
            .background(Color.Blue)
            .constrainAs(box3) {
                top.linkTo(parent.top)
            }
    )
}
```

---

## 🎨 Типы цепочек (`ChainStyle`)

|Тип|Описание|
|---|---|
|`Spread`|(По умолчанию) Элементы **равномерно распределены**, крайние прижаты к краям.|
|`SpreadInside`|Элементы равномерно, но **крайние не прижаты к краям** — отступы снаружи.|
|`Packed`|Элементы **сжаты вместе**, можно управлять положением через bias.|

### 🔍 Пример `Packed`:

```kotlin
createHorizontalChain(box1, box2, box3, chainStyle = ChainStyle.Packed)

constrainAs(box1) {
    top.linkTo(parent.top)
    start.linkTo(parent.start)
    end.linkTo(parent.end)
    horizontalBias = 0.0f // сдвинуто влево
}
```

---

## 📐 Вертикальная цепочка

Работает аналогично:

```kotlin
val (a, b, c) = createRefs()
createVerticalChain(a, b, c, chainStyle = ChainStyle.Spread)
```

Каждый элемент должен быть привязан по `top` и `bottom`.

---

## ⚠️ Важно

1. Все элементы цепочки должны быть **связаны по одной оси** (`start/end` или `top/bottom`).
    
2. Расстояние между элементами можно задавать через `margin` в `linkTo(...)`.
    
3. Bias у крайнего элемента позволяет **смещать всю цепочку** внутри родителя при стиле `Packed`.
    

---

## 🧠 Когда использовать chain?

- Когда тебе нужно **одинаковое расстояние** между элементами.
    
- Когда нужно **сжать несколько компонентов ближе друг к другу**, но всё равно держать их в центре или сдвигать.
    
- Альтернатива `Row` или `Column`, когда требуется **позиционирование с ограничениями**.
    

---

Если хочешь, могу привести пример с цепочкой карточек, кнопок или даже иконок — подскажи, в каком контексте тебе актуально.