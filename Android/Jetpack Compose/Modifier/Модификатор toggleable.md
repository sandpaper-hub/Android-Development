Модификатор `Modifier.toggleable` в Jetpack Compose даёт любой композируемой функции «поведение переключателя» — то есть обработку нажатий, изменение состояния между `true`/`false` и настройку индикации (например, ripple-эффекта). Он часто используется для создания собственных реализаций [чекбоксов](<Checkbox и TriStateCheckBox>), переключателей и радиокнопок.

---

## 1. Основные параметры

```kotlin
fun Modifier.toggleable(
    value: Boolean,
    onValueChange: (Boolean) -> Unit,
    enabled: Boolean = true,
    role: Role? = null,
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    indication: Indication? = LocalIndication.current
): Modifier
```

- **value** – текущее логическое состояние переключателя (`true` = включено, `false` = выключено).  
- **onValueChange** – лямбда, которая вызывается при каждом клике, с новым значением. Обычно внутри вы обновляете состояние через `mutableStateOf`.  
- **enabled** – если `false`, отключает обработку нажатий и визуальную индикацию.  
- **role** – семантическая роль (`Role.Checkbox`, `Role.Switch`, `Role.RadioButton` и т. д.) для accessibility (TalkBack и т. п.).  
- **interactionSource** – поток событий взаимодействия (`Pressed`, `Dragged` и т. д.), нужен для управления ripple-эффектом или кастомных анимаций.  
- **indication** – визуальная индикация клика (по умолчанию ripple из `LocalIndication`).

---

## 2. Простой пример

Создадим квадрат, который при нажатии меняет цвет и состояние «включено/выключено»:

```kotlin
@Composable
fun ToggleableBox() {
    var checked by remember { mutableStateOf(false) }

    Box(
        modifier = Modifier
            .size(100.dp)
            .toggleable(
                value = checked,
                onValueChange = { checked = it },
                role = Role.Checkbox
            )
            .background(if (checked) Color.Green else Color.Gray),
        contentAlignment = Alignment.Center
    ) {
        Text(if (checked) "ON" else "OFF")
    }
}
```

Здесь:
1. `toggleable` оборачивает `Box` и делает его «кликабельным» с переключением состояния.  
2. Мы задали `role = Role.Checkbox`, чтобы экранный диктор правильно называл элемент «чекбокс».  
3. Изменяем фон в зависимости от `checked`.

---

## 3. Кастомный чекбокс с иконкой

Можно сделать свой «чекбокс» с иконками:

```kotlin
@Composable
fun CustomCheckbox(
    checked: Boolean,
    onCheckedChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier
) {
    Row(
        modifier = modifier
            .toggleable(
                value = checked,
                onValueChange = onCheckedChange,
                role = Role.Checkbox
            )
            .padding(8.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Icon(
            imageVector = if (checked) Icons.Default.CheckBox else Icons.Default.CheckBoxOutlineBlank,
            contentDescription = if (checked) "Включено" else "Выключено"
        )
        Spacer(Modifier.width(8.dp))
        Text("Выбрать опцию")
    }
}
```

Использование:

```kotlin
var isOptionSelected by remember { mutableStateOf(false) }

CustomCheckbox(
    checked = isOptionSelected,
    onCheckedChange = { isOptionSelected = it }
)
```

---

## 4. Индикация и кастомные эффекты

По умолчанию `toggleable` применяет ripple-эффект при нажатии. Если вы хотите убрать или заменить его:

```kotlin
.toggleable(
    value = checked,
    onValueChange = { checked = it },
    indication = null, // без ripple
    interactionSource = remember { MutableInteractionSource() }
)
```

А чтобы подобрать свой эффект, передайте нужный `Indication` из библиотеки или свой.

---

## 5. Доступность (Accessibility)

- **role** (из `androidx.compose.ui.semantics.Role`) сообщает экранным дикторам, что это переключатель нужного типа.  
- `toggleable` автоматически добавляет в семантику: текущее состояние (`checked`/`unchecked`), возможность переключения и обработчик клика.  

Это облегчает тестирование и поддержку людей с ограничениями зрения.

---

### Когда использовать

- **Кастомные элементы** на основе переключателя: чекбоксы, свитчи, радиокнопки.  
- **Группировка клика и состояния**: когда рядом с индикатором (иконкой) есть текст, и вы хотите, чтобы клик на любом месте переключал состояние.  
- **Избегать дублирования** кода: вместо явной обработки `clickable {}` плюс `semantics {}` используйте готовый `toggleable`.

---

С помощью `Modifier.toggleable` можно легко создать собственные, адаптированные под дизайн приложения переключатели с полной поддержкой Material-индикации и accessibility.