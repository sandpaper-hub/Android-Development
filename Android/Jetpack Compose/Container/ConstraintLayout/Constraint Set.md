`ConstraintSet` в Jetpack Compose — это способ **отделить описание ограничений от самого UI**. Он особенно полезен, когда:

- ты хочешь **динамически менять расположение элементов**
    
- ты **разделяешь логику компоновки и визуализацию**
    
- тебе нужно **переиспользовать одни и те же компоненты с разными ограничениями**
    

---

## 🔧 Как работает `ConstraintSet`

Вместо того чтобы вызывать `constrainAs(...)` внутри `ConstraintLayout`, ты создаёшь **отдельный объект `ConstraintSet`**, в котором описываются связи между элементами по `layoutId`.

---

## 🧱 Пример использования

```kotlin
val constraints = ConstraintSet {
    val redBox = createRefFor("redBox")
    val yellowBox = createRefFor("yellowBox")

    constrain(redBox) {
        top.linkTo(parent.top)
        start.linkTo(parent.start)
    }

    constrain(yellowBox) {
        top.linkTo(redBox.bottom, margin = 16.dp)
        start.linkTo(parent.start)
    }
}

ConstraintLayout(constraintSet = constraints, modifier = Modifier.fillMaxSize()) {
    Box(
        modifier = Modifier
            .size(100.dp)
            .background(Color.Red)
            .layoutId("redBox")
    )
    Box(
        modifier = Modifier
            .size(100.dp)
            .background(Color.Yellow)
            .layoutId("yellowBox")
    )
}
```

---

## 💡 Ключевые элементы

|Элемент|Назначение|
|---|---|
|`ConstraintSet {}`|Контейнер с описанием всех ограничений|
|`createRefFor(id)`|Создаёт ссылку по `layoutId`|
|`.layoutId("...")`|Обязателен для связывания элемента с правилом в `ConstraintSet`|
|`constraintSet = ...`|Заменяет `constrainAs` в `ConstraintLayout`|

---

## 🔄 Динамическое обновление ограничений

Ты можешь **сменить `ConstraintSet`** на лету:

```kotlin
var expanded by remember { mutableStateOf(false) }

val constraints = if (expanded) expandedConstraints else collapsedConstraints

ConstraintLayout(constraintSet = constraints) {
    // ...
}
```

Это похоже на анимации `MotionLayout` — можно анимировать переход между двумя `ConstraintSet`'ами.

---

## 🧩 Когда `ConstraintSet` особенно полезен

- У тебя **большой и сложный layout**, и ты хочешь вынести компоновку в отдельную структуру
    
- Ты хочешь **переиспользовать один и тот же UI с разными позициями**
    
- Для **адаптации к разным экранам**, например планшет vs телефон
    
- Для **анимированных переходов** между состояниями
    

---

## ❗ Важно

- У каждого `Modifier` в `ConstraintLayout` должен быть `.layoutId("...")`, если используется `ConstraintSet`
    
- Если `layoutId` не совпадает с `createRefFor(...)`, ограничения просто не применятся