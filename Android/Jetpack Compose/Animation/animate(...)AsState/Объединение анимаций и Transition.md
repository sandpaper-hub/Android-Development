В **Jetpack Compose** объединение анимаций удобно делать через **`Transition` API** — оно позволяет синхронно анимировать несколько свойств, которые зависят от одного состояния.  
Это полезно, когда, например, при изменении флага `expanded` ты хочешь анимировать **размер, цвет, прозрачность и скругление углов** одновременно и согласованно.

---

## 1. Что такое `Transition`

`Transition` — это объект, который:

- Связан с **одним состоянием** (`targetState`).
    
- Управляет **всеми анимациями**, которые относятся к этому состоянию.
    
- Гарантирует, что изменения происходят **согласованно по времени**.
    

---

## 2. Как создать `Transition`

```kotlin
val transition = updateTransition(
    targetState = expanded,
    label = "boxTransition"
)
```

- `targetState` — состояние, определяющее, в каком виде должен быть элемент (например, `true` = раскрыт, `false` = свернут).
    
- `label` — подпись для отладки.
    

---

## 3. Анимация нескольких свойств

```kotlin
@Composable
fun CombinedAnimationsExample() {
    var expanded by remember { mutableStateOf(false) }

    val transition = updateTransition(targetState = expanded, label = "boxTransition")

    val size by transition.animateDp(label = "size") { state ->
        if (state) 200.dp else 100.dp
    }

    val color by transition.animateColor(label = "color") { state ->
        if (state) Color.Green else Color.Gray
    }

    val cornerRadius by transition.animateDp(label = "cornerRadius") { state ->
        if (state) 16.dp else 0.dp
    }

    Box(
        modifier = Modifier
            .size(size)
            .background(color, shape = RoundedCornerShape(cornerRadius))
            .clickable { expanded = !expanded }
    )
}
```

🔹 Здесь все три свойства (`size`, `color`, `cornerRadius`) меняются синхронно при изменении `expanded`.

---

## 4. Плюсы `Transition` по сравнению с `animate*AsState`

|`animate*AsState`|`Transition`|
|---|---|
|Управляет одной анимацией|Управляет несколькими анимациями одновременно|
|Проще для мелких эффектов|Удобен для комплексных изменений|
|Не группирует анимации|Гарантирует синхронность и согласованность|

---

## 5. Когда использовать

- При **смене состояний UI**, когда меняется несколько свойств.
    
- Для **анимированных карточек**, кнопок, панелей.
    
- Для **переходов** между состояниями (открыто/закрыто, активно/неактивно).
    
- Когда важно, чтобы все анимации стартовали и заканчивались вместе.

