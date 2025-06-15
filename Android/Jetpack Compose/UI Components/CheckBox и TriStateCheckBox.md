`Checkbox` в Jetpack Compose — это готовый компонент для двоичного выбора (установлен/снят флажок) по гайдлайнам Material Design 3. Ниже разберём его ключевые возможности и примеры использования.

---
## 1. Базовый пример

```kotlin
@Composable
fun SimpleCheckbox() {
    var checked by remember { mutableStateOf(false) }

    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier.padding(16.dp)
    ) {
        Checkbox(
            checked = checked,
            onCheckedChange = { checked = it }
        )
        Spacer(Modifier.width(8.dp))
        Text(text = if (checked) "Выбрано" else "Не выбрано")
    }
}
```

- **checked** — текущее состояние (`true`/`false`).
    
- **onCheckedChange** — вызывается при клике с новым значением.
    

---

## 2. Основные параметры

| Параметр                             | Описание                                                                                                           |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| `checked: Boolean`                   | Логическое состояние флажка.                                                                                       |
| `onCheckedChange: (Boolean) -> Unit` | Лямбда, в которой вы обычно обновляете своё состояние.                                                             |
| `enabled: Boolean`                   | Если `false`, флажок не кликабелен и выглядит «задизэйбленным».                                                    |
| `colors: CheckboxColors`             | Определяет цвета для разных состояний: отмечен/не отмечен и фокус/не в фокусе (через `CheckboxDefaults.colors()`). |

### Пример с кастомными цветами

```kotlin
Checkbox(
    checked = checked,
    onCheckedChange = { checked = it },
    colors = CheckboxDefaults.colors(
        checkedColor = MaterialTheme.colorScheme.primary,
        uncheckedColor = MaterialTheme.colorScheme.onSurfaceVariant,
        checkmarkColor = MaterialTheme.colorScheme.onPrimary
    )
)
```

---

## 3. Сопровождение текста и кликабельность по всей строке

Обычно вместе с `Checkbox` ставят текст, и хотят, чтобы клик по тексту тоже менял состояние. Для этого оборачивают в [toggleable](<Модификатор toggleable>) или используют `ClickableText`:

```kotlin
Row(
    verticalAlignment = Alignment.CenterVertically,
    modifier = Modifier
        .fillMaxWidth()
        .toggleable(
            value = checked,
            onValueChange = { checked = it },
            role = Role.Checkbox
        )
        .padding(16.dp)
) {
    Checkbox(
        checked = checked,
        onCheckedChange = null // управление через toggleable
    )
    Spacer(Modifier.width(8.dp))
    Text("Принять условия")
}
```

Здесь `onCheckedChange = null` в самом `Checkbox` — чтобы избежать двойного срабатывания, а логику клика берёт на себя `toggleable`.

---

## 4. Трёхсостоянный флажок (Tri-State)

Если нужен «неопределённый» (indeterminate) режим, используйте `TriStateCheckbox`:

```kotlin
import androidx.compose.material3.TriStateCheckbox
import androidx.compose.material3.ToggleableState

var state by remember { mutableStateOf(ToggleableState.Off) }

TriStateCheckbox(
    state = state,
    onClick = {
        state = when (state) {
            ToggleableState.Off -> ToggleableState.On
            ToggleableState.On  -> ToggleableState.Indeterminate
            ToggleableState.Indeterminate -> ToggleableState.Off
        }
    }
)
```

- **state: ToggleableState** — `On`, `Off` или `Indeterminate`.
    
- **onClick** — обработка переключения между тремя состояниями.
    

---

## 5. Доступность и семантика

`Checkbox` автоматически добавляет семантическую информацию для TalkBack (экранного диктора): что это флажок, текущее состояние и возможность переключения. При необходимости можно дополнить семантику:

```kotlin
Checkbox(
    checked = checked,
    onCheckedChange = { checked = it },
    modifier = Modifier.semantics {
        contentDescription = "Подтвердить согласие"
    }
)
```

---

### Итоги

- **Прост в использовании**: достаточно задать `checked` и `onCheckedChange`.
    
- **Гибок в стилизации**: через `CheckboxDefaults.colors` и `enabled`.
    
- **Расширяем**: подключайте `toggleable` для клика по тексту, или используйте `TriStateCheckbox` для трёх состояний.
    
- **Доступность**: поддерживается из коробки, можно дополнять через `semantics`.
    

Используйте `Checkbox` для любых форм, где требуется бинарный выбор, и он органично впишется в интерфейс Compose-приложения.