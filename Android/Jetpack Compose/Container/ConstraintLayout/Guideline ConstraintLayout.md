В `ConstraintLayout` для Jetpack Compose `Guideline` — это **невидимая линия**, которую можно использовать как **опорную точку** для размещения других элементов. Она особенно полезна, если ты хочешь выровнять элементы **относительно доли от родителя**, **фиксированного расстояния** или **процентной позиции**.

---

## 🧱 Что такое `Guideline`

`Guideline` — это вспомогательный элемент внутри `ConstraintLayout`, который сам **не отображается**, но может участвовать в `linkTo`, `start.linkTo()`, `top.linkTo()` и т.д.

---

## 📌 Как создать Guideline

Создаётся через:

```kotlin
val guideline = createGuidelineFromStart(fraction = 0.25f) // 25% от начала
```

Или варианты:

|Метод|Описание|
|---|---|
|`createGuidelineFromStart(fraction = ...)`|вертикальная линия от **левого края**|
|`createGuidelineFromEnd(fraction = ...)`|вертикальная линия от **правого края**|
|`createGuidelineFromTop(fraction = ...)`|горизонтальная линия от **верхнего края**|
|`createGuidelineFromAbsoluteLeft(...)`|то же, но игнорирует RTL|
|`createGuidelineFromStart(offset = 32.dp)`|фиксированный отступ от левого края|

---

## ✅ Пример использования

```kotlin
@Composable
fun ConstraintLayoutWithGuideline() {
    ConstraintLayout(
        modifier = Modifier.fillMaxSize()
    ) {
        val guideline = createGuidelineFromStart(0.3f) // 30% от ширины родителя
        val (text) = createRefs()

        Text(
            "Пример с Guideline",
            modifier = Modifier
                .constrainAs(text) {
                    start.linkTo(guideline)
                    top.linkTo(parent.top)
                }
        )
    }
}
```

📌 В этом примере:

- `Guideline` проходит на 30% от левого края.
    
- `Text` выравнивается по этой линии.
    

---

## 🧠 Когда использовать Guideline

- Нужно **универсальное выравнивание**, особенно при адаптивных размерах экрана.
    
- Хочешь, чтобы несколько элементов начинались с **одинаковой позиции**.
    
- Нужно имитировать **макет с колонками** или **сеткой**.
    
- Используется при **создании адаптивных UI** вместо `Spacer` или `Padding`.