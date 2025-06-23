**IconButton** и **IconToggleButton** — это специализированные кнопки в Jetpack Compose, предназначенные для работы с иконками. Они облегчают создание компактных интерактивных элементов, которые визуально фокусируются на иконке.

---

## 1. IconButton

- **Поведение**: обычная кнопка, в которой в качестве контента выступает иконка. При нажатии выполняет действие и не хранит состояния “включено/выключено”.
- **Размеры**: по умолчанию 48 × 48 dp (минимальный рекомендуемый размер для Material Design).
- **Использование**:
  ```kotlin
  @Composable
  fun MyIconButton(onRefresh: () -> Unit) {
    IconButton(onClick = onRefresh) {
      Icon(
        imageVector = Icons.Default.Refresh,
        contentDescription = "Обновить данные"
      )
    }
  }
  ```
  - `onClick` — лямбда для обработки нажатия.
  - Внутри `IconButton` вы кладёте любую иконку (`Icon`, `Image`, кастомный `Canvas` и т. д.).
  - Обязательно указывайте `contentDescription` для accessibility.

### 1.1. Настройка цветов и размеров

```kotlin
IconButton(
  onClick = { /*...*/ },
  modifier = Modifier.size(56.dp),               // размер кнопки
  enabled = isEnabled,                           // доступность
) {
  Icon(
    imageVector = Icons.Default.Favorite,
    contentDescription = "Добавить в избранное",
    tint = if (isEnabled) Color.Red else Color.Gray
  )
}
```

- `modifier.size()` задаёт размер области клика.
- Цвет иконки можно менять через параметр `tint`.

---

## 2. IconToggleButton

- **Поведение**: кнопка-переключатель, хранит булевое состояние: “включено” (`checked = true`) или “выключено” (`false`).
- **Внешне** выглядит как иконка, меняющаяся в зависимости от состояния (например, пустое сердце → заполненное).
- **Использование**:
  ```kotlin
  @Composable
  fun FavoriteToggle() {
    var isFavorite by remember { mutableStateOf(false) }

    IconToggleButton(
      checked = isFavorite,
      onCheckedChange = { newValue -> isFavorite = newValue }
    ) {
      Icon(
        imageVector = if (isFavorite) Icons.Default.Favorite else Icons.Default.FavoriteBorder,
        contentDescription = if (isFavorite) "В избранном" else "Не в избранном"
      )
    }
  }
  ```
  - `checked` — текущее состояние переключателя.
  - `onCheckedChange` — вызывается с новым состоянием при клике.
  - Контент блока меняется в зависимости от `checked`.

### 2.1. Стилизация

```kotlin
IconToggleButton(
  checked = isOn,
  onCheckedChange = { isOn = it },
  modifier = Modifier.size(40.dp),
) {
  // Пример с кастомным цветом рамки в состоянии “off”
  Box(
    modifier = Modifier
      .background(
        color = if (isOn) MaterialTheme.colors.primary else Color.Transparent,
        shape = CircleShape
      )
      .padding(8.dp)
  ) {
    Icon(
      painter = painterResource(R.drawable.ic_bell),
      contentDescription = null
    )
  }
}
```

---

## 3. Ключевые отличия и когда использовать

| Характеристика        | IconButton                            | IconToggleButton                       |
|-----------------------|---------------------------------------|----------------------------------------|
| Состояние             | Без собственного состояния            | Хранит булевое состояние `checked`     |
| Коллбэк               | `onClick: () -> Unit`                 | `onCheckedChange: (Boolean) -> Unit`   |
| Визуальная смена icon | Нужно делать вручную в `onClick`/state| Логичнее менять контент по `checked`   |
| Применение            | Обычные действия (“обновить”, “удалить”) | Переключатели (“избранное”, “уведомления”) |

---

## 4. Best practices

1. **Accessibility**  
   – Давайте понятное `contentDescription`.  
   – Для чисто декоративных иконок указывайте `contentDescription = null`.

2. **Размер кликабельной области**  
   – Минимум 48 × 48 dp для комфортного касания.

3. **Семантика переключателя**  
   – Для `IconToggleButton` старайтесь менять не только визуал, но и описание, чтобы TalkBack читал актуальное состояние.

4. **State hoisting**  
   – Выносите состояние (`checked`/`onCheckedChange`) наружу (в родительский `ViewModel` или Composable), чтобы компонент оставался переиспользуемым.
