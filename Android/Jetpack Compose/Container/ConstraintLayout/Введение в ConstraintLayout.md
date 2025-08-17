В **Jetpack Compose** существует специальный layout под названием `ConstraintLayout`, который позволяет гибко размещать элементы на экране с помощью **ограничений** — аналогично тому, как это работает в XML-версии ConstraintLayout.

### Когда использовать `ConstraintLayout`?
- Когда необходимо **точное позиционирование** элементов друг относительно друга.
- При сложной компоновке, где обычные `Row`, `Column`, `Box` становятся громоздкими или не позволяют выразить зависимость одного элемента от другого.

---

## 🔧 Подключение зависимости
`ConstraintLayout` не входит в основной compose-пакет, его нужно подключать отдельно:

```kotlin
dependencies {
    implementation("androidx.constraintlayout:constraintlayout-compose:1.0.1")
}
```

(Проверь последнюю версию на [Maven](https://mvnrepository.com/artifact/androidx.constraintlayout/constraintlayout-compose))

---

## 🧱 Пример базового использования

```kotlin
import androidx.constraintlayout.compose.ConstraintLayout

@Composable
fun ConstraintLayoutExample() {
    ConstraintLayout(modifier = Modifier.fillMaxSize()) {
        // Создаем ссылки на элементы
        val (button, text) = createRefs()

        Button(
            onClick = { /*...*/ },
            modifier = Modifier.constrainAs(button) {
                top.linkTo(parent.top, margin = 16.dp)
                start.linkTo(parent.start, margin = 16.dp)
            }
        ) {
            Text("Нажми")
        }

        Text(
            text = "Пример текста",
            modifier = Modifier.constrainAs(text) {
                top.linkTo(button.bottom, margin = 16.dp)
                start.linkTo(button.start)
            }
        )
    }
}
```

---

## 📌 Основные понятия

| Команда                      | Описание                                                  |
|-----------------------------|-----------------------------------------------------------|
| `createRefs()`              | Создает ссылки для элементов внутри `ConstraintLayout`    |
| `constrainAs()`             | Применяет ограничения к элементу                          |
| `linkTo(...)`               | Указывает, к чему привязать сторону элемента              |
| `parent`                    | Ссылка на сам `ConstraintLayout`                         |
| `createGuidelineFromTop(…)` | Создает направляющую (guideline)                         |
| `barrier`                   | Объединяет несколько элементов в одну "границу"           |

---
## 📌 Взаимная установка ограничений (двойные ограничения)

Это когда элемент **одновременно привязывается** к **двум сторонам** (например, `start.linkTo(...)` и `end.linkTo(...)`) или `top` и `bottom`.

### Пример:

```kotlin
Text(
    "Центрированный текст",
    modifier = Modifier.constrainAs(text) {
        start.linkTo(parent.start)
        end.linkTo(parent.end)
        top.linkTo(parent.top)
        bottom.linkTo(parent.bottom)
    }
)
```

🔹 Здесь текст **будет расположен точно по центру** родительского `ConstraintLayout` как по горизонтали, так и по вертикали.

---

## 🎯 Смещение (Bias)

Когда элемент привязан **к двум сторонам**, можно управлять его **смещением** между ними.

Значения bias:

| Значение | Положение               |
| -------- | ----------------------- |
| `0f`     | В начале                |
| `0.5f`   | В центре (по умолчанию) |
| `1f`     | В конце                 |

### Пример со смещением влево:

```kotlin
Box(  
    modifier = Modifier  
        .size(100.dp)  
        .background(Color.Yellow)  
        .constrainAs(box2) {  
            top.linkTo(box1.bottom, margin = 16.dp)  
            linkTo(parent.start, parent.end, bias = 0.75f)  
        }  
)
```

🔹 Этот `Box` будет ближе к **концу (end)** — то есть правее центра.

---

## ⚙ Смещение вручную через Modifier.offset

Если тебе нужно **смещение в пикселях или dp**, можно использовать `Modifier.offset` **внутри** `constrainAs`, **но будь осторожен**: оно применяется **после расчёта layout**, а не участвует в ограничениях.

```kotlin
Text(
    "Со смещением",
    modifier = Modifier
        .offset(x = 8.dp, y = 0.dp) // Сдвигаем вправо на 8dp
        .constrainAs(text) {
            top.linkTo(parent.top)
            start.linkTo(parent.start)
        }
)
```

---

## 🔁 Взаимное позиционирование между элементами

Ты можешь задать ограничения **между элементами**, и каждый из них может реагировать на положение другого.

### Пример:

```kotlin
val (box1, box2) = createRefs()

Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Red)
        .constrainAs(box1) {
            start.linkTo(parent.start)
            top.linkTo(parent.top)
        }
)

Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Blue)
        .constrainAs(box2) {
            top.linkTo(box1.bottom, margin = 16.dp)
            start.linkTo(box1.start)
        }
)
```

🔹 `box2` будет расположен **под `box1`** с отступом 16.dp.

- [[Guideline ConstraintLayout]]
- [[Barrirer ConstraintLayout]]
- [[Constraint Set]]
- [[Chain]]