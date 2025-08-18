`AnimatedVisibility` — это удобный **Composable** в Jetpack Compose, который анимирует появление и исчезновение UI-элементов.  
В отличие от `animate*AsState`, который работает с отдельными значениями, `AnimatedVisibility` управляет **всем компонентом целиком**: можно плавно показать или спрятать элемент.

---

## 1. Сигнатура

```kotlin
@Composable
fun AnimatedVisibility(
    visible: Boolean,
    modifier: Modifier = Modifier,
    enter: EnterTransition = fadeIn(),
    exit: ExitTransition = fadeOut(),
    label: String = "AnimatedVisibility",
    content: @Composable AnimatedVisibilityScope.() -> Unit
)
```

---

## 2. Основные параметры

|Параметр|Что делает|
|---|---|
|**`visible`**|Показывать или скрывать элемент|
|**`enter`**|Анимация при появлении (`fadeIn`, `slideIn`, `expandIn`)|
|**`exit`**|Анимация при исчезновении (`fadeOut`, `slideOut`, `shrinkOut`)|
|**`content`**|Сам UI, который будет анимироваться|

---

## 3. Базовый пример

```kotlin
@Composable
fun AnimatedVisibilityExample() {
    var visible by remember { mutableStateOf(true) }

    Column {
        Button(onClick = { visible = !visible }) {
            Text("Toggle")
        }

        AnimatedVisibility(visible = visible) {
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.Magenta)
            )
        }
    }
}
```

🔹 Кнопка переключает флаг `visible`, и `Box` плавно появляется или исчезает.

---

## 4. Кастомизация анимаций

Можно комбинировать разные эффекты:

```kotlin
AnimatedVisibility(
    visible = visible,
    enter = slideInVertically(initialOffsetY = { -it }) + fadeIn(),
    exit = slideOutVertically(targetOffsetY = { it }) + fadeOut()
) {
    Box(
        Modifier
            .size(120.dp)
            .background(Color.Green)
    )
}
```

- **`slideInVertically`** — сдвиг сверху.
    
- **`slideOutVertically`** — уход вниз.
    
- **`fadeIn`/`fadeOut`** — прозрачность.
    
- Оператор `+` позволяет объединять несколько эффектов.
    

---

## 5. Enter и Exit Transition — варианты

### Вход (`enter`)

- `fadeIn()` — плавное появление.
    
- `expandIn()` — «разворачивание» из маленького размера.
    
- `slideInVertically()` / `slideInHorizontally()` — въезд с края.
    

### Выход (`exit`)

- `fadeOut()` — плавное исчезновение.
    
- `shrinkOut()` — сжатие до нуля.
    
- `slideOutVertically()` / `slideOutHorizontally()` — выезд за границу.
    

---

## 6. Где использовать

- Панели и меню (например, `DropdownMenu` можно сделать на `AnimatedVisibility`).
    
- Сообщения (toast, snackbar).
    
- Всплывающие подсказки.
    
- Контент, который должен плавно входить/выходить при смене состояния.
    
## 7. Сложный пример

 1. Чистый fade 
 2. slide + fade
 3. expand + shrink.  

Каждый блок включается/выключается своей кнопкой, а тайминг и тип анимации настроены через `animationSpec`.

```kotlin
import androidx.compose.animation.*
import androidx.compose.animation.core.*
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp

@OptIn(ExperimentalAnimationApi::class)
@Composable
fun AnimatedVisibilityDemo(modifier: Modifier = Modifier) {
    var visibleFade by rememberSaveable { mutableStateOf(true) }
    var visibleSlide by rememberSaveable { mutableStateOf(true) }
    var visibleExpand by rememberSaveable { mutableStateOf(true) }

    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(24.dp)
    ) {
        // === 1) Только fade ===
        Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
            Text("1) fadeIn / fadeOut", style = MaterialTheme.typography.titleMedium)
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Button(onClick = { visibleFade = !visibleFade }) {
                    Text(if (visibleFade) "Скрыть" else "Показать")
                }
            }
            AnimatedVisibility(
                visible = visibleFade,
                enter = fadeIn(animationSpec = tween(durationMillis = 450, easing = LinearOutSlowInEasing)),
                exit  = fadeOut(animationSpec = tween(durationMillis = 300, easing = FastOutLinearInEasing)),
                label = "fade-only"
            ) {
                DemoCard("fade", Color(0xFFFF80AB))
            }
        }

        // === 2) Slide + Fade (сверху вниз / вниз наружу) ===
        Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
            Text("2) slide + fade", style = MaterialTheme.typography.titleMedium)
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Button(onClick = { visibleSlide = !visibleSlide }) {
                    Text(if (visibleSlide) "Скрыть" else "Показать")
                }
            }
            AnimatedVisibility(
                visible = visibleSlide,
                enter = slideInVertically(
                    // старт за пределами сверху
                    initialOffsetY = { fullHeight -> -fullHeight / 2 },
                    animationSpec = tween(500, delayMillis = 100, easing = FastOutSlowInEasing)
                ) + fadeIn(animationSpec = tween(500)),
                exit = slideOutVertically(
                    // уезжаем вниз
                    targetOffsetY = { fullHeight -> fullHeight / 2 },
                    animationSpec = tween(400, easing = FastOutLinearInEasing)
                ) + fadeOut(animationSpec = tween(300)),
                label = "slide-fade"
            ) {
                DemoCard("slide + fade", Color(0xFF82B1FF))
            }
        }

        // === 3) Expand + Shrink (c кастомной точки и пружиной) ===
        Column(verticalArrangement = Arrangement.spacedBy(8.dp)) {
            Text("3) expandIn + shrinkOut", style = MaterialTheme.typography.titleMedium)
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                Button(onClick = { visibleExpand = !visibleExpand }) {
                    Text(if (visibleExpand) "Скрыть" else "Показать")
                }
            }
            AnimatedVisibility(
                visible = visibleExpand,
                enter = expandIn(
                    // «разворачиваемся» из верхнего-левого угла
                    expandFrom = Alignment.TopStart,
                    animationSpec = spring(
                        dampingRatio = Spring.DampingRatioMediumBouncy,
                        stiffness = Spring.StiffnessLow
                    )
                ) + fadeIn(animationSpec = tween(200)),
                exit = shrinkOut(
                    // «сжимаемся» к центру
                    shrinkTowards = Alignment.Center,
                    animationSpec = tween(350, easing = LinearOutSlowInEasing)
                ) + fadeOut(animationSpec = tween(200)),
                label = "expand-shrink"
            ) {
                DemoCard("expand + shrink", Color(0xFFB9F6CA))
            }
        }
    }
}

@Composable
private fun DemoCard(text: String, color: Color) {
    Box(
        modifier = Modifier
            .fillMaxWidth()
            .height(96.dp)
            .background(color, RoundedCornerShape(16.dp))
            .padding(16.dp),
        contentAlignment = Alignment.CenterStart
    ) {
        Text(text = text, style = MaterialTheme.typography.titleLarge, color = Color.Black)
    }
}
```

что можно менять под себя:

- **тайминг:** в каждом `fadeIn/Out`, `slideIn/Out`, `expandIn/shrinkOut` через `animationSpec = tween(...)` или `spring(...)`;
    
- **направление и точку старта/финиша:** `initialOffsetY`, `targetOffsetY`, `expandFrom`, `shrinkTowards`;
    
- **комбинации эффектов:** складывай переходы оператором `+` (например, `slide + fade + scaleIn()`).

## Модификатор animateEnterExit()
[[Модификатор animateEnterExit()]]