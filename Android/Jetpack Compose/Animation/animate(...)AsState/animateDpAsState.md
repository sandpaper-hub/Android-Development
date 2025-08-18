animateDpAsState это **анимация одного значения типа `Dp`** с автоматическим переходом от текущего к целевому значению.

---

## 1. Что это такое

`animateDpAsState` — это функция, которая:

- Принимает **`targetValue: Dp`** — к какому значению нужно прийти;
    
- Возвращает **`State<Dp>`**, которое можно читать в UI (оно плавно изменяется);
    
- Под капотом запускает анимацию при каждом изменении целевого значения;
    
- Работает **декларативно**: тебе не нужно вручную запускать или останавливать анимацию.
    

---

## 2. Сигнатура

```kotlin
@Composable
fun animateDpAsState(
    targetValue: Dp,
    animationSpec: AnimationSpec<Dp> = spring(),
    visibilityThreshold: Dp = Dp.VisibilityThreshold,
    finishedListener: ((Dp) -> Unit)? = null,
    label: String = "DpAnimation"
): State<Dp>
```

---

## 3. Параметры

| Параметр                  | Что делает                                         |
| ------------------------- | -------------------------------------------------- |
| **`targetValue`**         | Конечное значение, к которому идёт анимация        |
| **`animationSpec`**       | Тип анимации: `tween()`, `spring()`, `keyframes()` |
| **`visibilityThreshold`** | Порог, при котором анимация считается завершённой  |
| **`finishedListener`**    | Коллбэк, вызываемый после окончания анимации       |
| **`label`**               | Отладочная подпись                                 |

---

## 4. Пример использования

```kotlin
@Composable
fun AnimateDpExample() {
    var big by remember { mutableStateOf(false) }
    val size by animateDpAsState(
        targetValue = if (big) 200.dp else 100.dp,
        animationSpec = tween(durationMillis = 1000)
    )

    Box(
        modifier = Modifier
            .size(size)
            .background(Color.Magenta)
            .clickable { big = !big }
    )
}
```

🔹 Здесь `size` — это **анимированное состояние** (`State<Dp>`), которое плавно меняется между 100.dp и 200.dp.

---

## 5. Ключевые моменты

1. **`Dp` → только для размеров и отступов**  
    — Можно анимировать ширину, высоту, padding, offset и т. д.
    
2. **Не управляется вручную**  
    — Если нужен полный контроль (пауза, продолжение, отмена), лучше использовать `Animatable<Dp>`.
    
3. **Работает только внутри Compose**  
    — Это `@Composable` функция, она не предназначена для View или вне UI-дерева.
    
4. **Под капотом — `State`**  
    — Значение всегда доступно как `size.value` или через `by` для деструктуризации.
    
