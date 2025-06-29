RadioButton — это элемент интерфейса для выбора **одной** опции из набора. Обычно они используются группами: при выборе одного варианта остальные автоматически сбрасываются.

---

## 1. Основная концепция

- **Эксклюзивный выбор**: из группы RadioButton можно отметить только один элемент.
    
- **Группировка**: в классическом View–подходе используют `<RadioGroup>` для контейнера, в Jetpack Compose — вручную отслеживают общее состояние.
    

---

## 2. RadioButton в Jetpack Compose

### 2.1. Подключение

```kotlin
import androidx.compose.material.RadioButton
import androidx.compose.material.Text
import androidx.compose.foundation.layout.Row
import androidx.compose.runtime.*
```

### 2.2. Пример простой группы

```kotlin
@Composable
fun GenderSelection() {
    // состояние выбранной опции
    var selectedOption by remember { mutableStateOf("Male") }
    val options = listOf("Male", "Female", "Other")

    Column {
        options.forEach { option ->
            Row(
                verticalAlignment = Alignment.CenterVertically,
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(8.dp)
                    .clickable { selectedOption = option }
            ) {
                RadioButton(
                    selected = (option == selectedOption),
                    onClick = { selectedOption = option }
                )
                Spacer(modifier = Modifier.width(8.dp))
                Text(text = option)
            }
        }
    }
}
```

- `selectedOption` хранит текущий выбор.
    
- Для каждой опции рисуем `RadioButton` и `Text`.
    
- По клику (либо на саму кнопку, либо на строку) обновляем `selectedOption`.
    

### 2.3. Настройка цвета и размера

```kotlin
RadioButton(
    selected = isSelected,
    onClick = { /*...*/ },
    colors = RadioButtonDefaults.colors(
        selectedColor = MaterialTheme.colors.primary,
        unselectedColor = MaterialTheme.colors.onSurface.copy(alpha = 0.6f),
        disabledColor = Color.Gray
    ),
    modifier = Modifier.size(24.dp)
)
```

- Через `RadioButtonDefaults.colors` можно задать собственные оттенки.
    
- Размер управляется через `modifier.size()`.
    
---

## 3. Лучшие практики

1. **Accessibility**: добавляйте `contentDescription` или используйте семантику для TalkBack.
    
2. **Кликабельная область**: оборачивайте группу кнопки и текста в `clickable`, чтобы пользователь легко мог выбрать опцию.
    
3. **Состояние**: в Compose храните единое состояние для группы, а не состояние каждого `RadioButton` по-отдельности.
    
4. **Кастомизация**: при необходимости создавайте собственные RadioButton с помощью `Canvas` или `Box`, но чаще достаточно стандартного.
    

---

Если вам нужно более подробно обсудить конкретный сценарий (например, динамическое создание списка опций, валидацию ввода или тестирование), дайте знать!