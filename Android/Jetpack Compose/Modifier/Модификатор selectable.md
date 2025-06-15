`Modifier.selectable` даёт любому компоновщику (Layout) или элементу UI возможность вести себя как элемент выбора (selected/unselected) с полной семантикой для accessibility — например, для радиокнопок или пунктов списка.

---

## 1. Когда использовать `selectable`

- Когда нужно отметить один из набора элементов (радиокнопки, пункты меню, элементы списка).
    
- Если важно, чтобы экранный диктор (TalkBack) понимал, что элемент может быть выбран и какое у него текущее состояние.
    
- В сочетании с `Modifier.selectableGroup()` для группировки элементов с единой логикой выбора.
    

---

## 2. Сигнатура

```kotlin
fun Modifier.selectable(
    selected: Boolean,
    onClick: () -> Unit,
    enabled: Boolean = true,
    role: Role? = null,
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    indication: Indication? = LocalIndication.current
): Modifier
```

- **selected** — текущее состояние (`true` = выбран).
    
- **onClick** — что делать при нажатии (обычно обновить своё `mutableStateOf`).
    
- **enabled** — если `false`, отключает взаимодействие и визуальную индикацию.
    
- **role** — семантическая роль из `androidx.compose.ui.semantics.Role` (`RadioButton`, `Tab`, `Switch` и т. п.) для accessibility.
    
- **interactionSource** и **indication** — как и в `clickable`, отвечают за ripple-эффекты и потоки взаимодействия.
    

---

## 3. Отличие от `clickable` и `toggleable`

|Модификатор|Семантика|Состояние|Обработка|
|---|---|---|---|
|`clickable`|просто клик|нет|`onClick()`|
|`toggleable`|переключатель (чекбокс)|булево|`onValueChange(Boolean)`|
|**`selectable`**|выбор (радио и т. п.)|булево|`onClick()`|

- `selectable` не предлагает логики переключения между `true`/`false` — вы сами меняете состояние в `onClick`.
    
- Семантика — это не просто клик, а «выбор элемента».
    

---

## 4. Пример простого выделяемого элемента

```kotlin
@Composable
fun HighlightableItem() {
    var selected by remember { mutableStateOf(false) }

    Box(
        modifier = Modifier
            .fillMaxWidth()
            .padding(8.dp)
            .selectable(
                selected = selected,
                onClick = { selected = !selected },
                role = Role.Checkbox, // или RadioButton, если нужно
            )
            .background(if (selected) Color.LightGray else Color.Transparent)
            .padding(16.dp)
    ) {
        Text("Нажми меня")
    }
}
```

Здесь при клике меняется фон, и TalkBack объявит элемент как «чекбокс, установлен/снят».

---

## 5. Группа радиокнопок с `selectableGroup`

```kotlin
@Composable
fun RadioGroupSample() {
    val options = listOf("Красный", "Зелёный", "Синий")
    var selectedOption by remember { mutableStateOf(options[0]) }

    Column(
        modifier = Modifier
            .selectableGroup() // объединяет дочерние selectable
            .padding(16.dp)
    ) {
        options.forEach { color ->
            Row(
                verticalAlignment = Alignment.CenterVertically,
                modifier = Modifier
                    .fillMaxWidth()
                    .selectable(
                        selected = (color == selectedOption),
                        onClick = { selectedOption = color },
                        role = Role.RadioButton
                    )
                    .padding(8.dp)
            ) {
                RadioButton(
                    selected = (color == selectedOption),
                    onClick = null // обработка через Modifier.selectable
                )
                Spacer(Modifier.width(8.dp))
                Text(text = color)
            }
        }
    }
}
```

- `selectableGroup()` добавляет общие семантические свойства к контейнеру.
    
- `RadioButton(onClick = null)` — чтобы не было двойного вызова, весь клик обрабатывает `Modifier.selectable`.
    

---

## 6. Кастомизация индикации и accessibility

- **Ripple**: заменить или отключить через `indication = null`.
    
- **InteractionSource**: передайте кастомный `MutableInteractionSource` для отслеживания состояния `Pressed`, `Dragged` и т. п.
    
- **role**: не забывайте указывать (`Role.RadioButton`, `Role.Tab`, `Role.Checkbox`), чтобы помощники правильно озвучивали элемент.
    
- **semantics { }**: дополнительно опишите `contentDescription`, если текст рядом не даёт полной информации.
    

---

Используя `Modifier.selectable`, вы получите понятную пользователю и доступную реализацию выбора элементов, гибкую в настройке и легко интегрируемую в составные UI-сценарии.