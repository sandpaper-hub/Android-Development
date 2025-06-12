`OutlinedTextField` — это версия текстового поля в стиле Material Design с чётким контуром вокруг области ввода. По сравнению с `TextField` (filled style), у него более «лёгкий» вид и он хорошо вписывается в минималистичные интерфейсы. Ниже разберём ключевые моменты и возможности.

---

## 1. Основные особенности

- **Контур (outline)**  
  Поле обрамлено рамкой, которая изменяет цвет при фокусе/ошибке.  
- **Плавающая метка (floating label)**  
  Лейбл «переезжает» наверх при вводе или фокусе.  
- **Placeholder**  
  Появляется, когда поле пустое и не в фокусе (под лейблом).  
- **Иконки и кнопки**  
  Можно добавить `leadingIcon` (слева) и `trailingIcon` (справа).  
- **Состояние ошибки**  
  Можно визуально выделить поле при валидации (красная рамка, текст подсказки).

---

## 2. Ключевые параметры

| Параметр                         | Описание                                                                                 |
|----------------------------------|------------------------------------------------------------------------------------------|
| `value: String`                  | Текущий текст в поле.                                                                    |
| `onValueChange: (String) -> Unit`| Лямбда для обновления состояния текста.                                                  |
| `label: @Composable (() -> Unit)?`      | Плавающая метка, например `Text("Email")`.                                             |
| `placeholder: @Composable (() -> Unit)?`| Текст-подсказка, пока поле пустое, например `Text("example@domain.com")`.              |
| `leadingIcon: @Composable (() -> Unit)?` | Иконка слева внутри поля.                                                             |
| `trailingIcon: @Composable (() -> Unit)?`| Иконка или кнопка справа (очистка, показать/скрыть пароль).                            |
| `isError: Boolean`               | Показывает поле в состоянии ошибки, если `true`.                                         |
| `singleLine: Boolean`            | Разрешить одну строку (обычно `true`).                                                   |
| `maxLines: Int`                  | Максимальное число строк.                                                                |
| `visualTransformation: VisualTransformation` | Для маскировки (пароль, форматирование).                                     |
| `keyboardOptions: KeyboardOptions`       | Настройки софт-клавиатуры (тип ввода, IME-Action).                                  |
| `keyboardActions: KeyboardActions`       | Обработка действий клавиатуры (Done, Next и т.п.).                                  |
| `colors: TextFieldColors`        | Цвета рамки, курсора, текста в разных состояниях (через `TextFieldDefaults.outlinedTextFieldColors()`). |
| `shape: Shape`                   | Форма углов контура (по умолчанию — `RoundedCornerShape(4.dp)`).                        |
| `enabled: Boolean`               | Включено ли поле (если `false`, контур и текст становятся менее контрастными).           |

---

## 3. Простой пример

```kotlin
@Composable
fun EmailInputField() {
    var email by remember { mutableStateOf("") }
    var isError by remember { mutableStateOf(false) }

    OutlinedTextField(
        value = email,
        onValueChange = {
            email = it
            isError = !android.util.Patterns.EMAIL_ADDRESS.matcher(it).matches()
        },
        label = { Text("Email") },
        placeholder = { Text("example@domain.com") },
        singleLine = true,
        isError = isError,
        leadingIcon = {
            Icon(Icons.Default.Email, contentDescription = null)
        },
        trailingIcon = {
            if (email.isNotEmpty()) {
                IconButton(onClick = { email = "" }) {
                    Icon(Icons.Default.Close, contentDescription = "Очистить")
                }
            }
        },
        keyboardOptions = KeyboardOptions(
            keyboardType = KeyboardType.Email,
            imeAction = ImeAction.Done
        ),
        keyboardActions = KeyboardActions(
            onDone = { /* Скрыть клавиатуру или выполнить отправку */ }
        ),
        colors = TextFieldDefaults.outlinedTextFieldColors(
            focusedBorderColor = if (isError) Color.Red else MaterialTheme.colorScheme.primary,
            unfocusedBorderColor = if (isError) Color.Red.copy(alpha = 0.5f) else MaterialTheme.colorScheme.onSurface.copy(alpha = 0.5f)
        ),
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp)
    )

    if (isError) {
        Text(
            text = "Некорректный email",
            color = Color.Red,
            style = MaterialTheme.typography.bodySmall,
            modifier = Modifier.padding(start = 16.dp, top = 4.dp)
        )
    }
}
```

**Пояснения:**
1. **Валидация**: флаг `isError` переключается в зависимости от формата email.  
2. **Цвета**: рамка меняет цвет при ошибке.  
3. **Иконки**: левая фиксирована, правая скрывается, если поле пустое.  
4. **Подсказка об ошибке**: отдельный `Text` ниже поля.

---

## 4. Кастомизация формы и цветов

Вы можете задать собственную форму рамки и другие цвета:

```kotlin
OutlinedTextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Пароль") },
    visualTransformation = PasswordVisualTransformation(),
    keyboardOptions = KeyboardOptions(imeAction = ImeAction.Done),
    colors = TextFieldDefaults.outlinedTextFieldColors(
        focusedBorderColor = MaterialTheme.colorScheme.secondary,
        cursorColor = MaterialTheme.colorScheme.secondary,
        focusedLabelColor = MaterialTheme.colorScheme.secondary
    ),
    shape = RoundedCornerShape(topStart = 8.dp, bottomEnd = 8.dp),
    modifier = Modifier.fillMaxWidth()
)
```

- **shape**: комбинируете скругления по углам.  
- **cursorColor** и **focusedLabelColor**: дополнительно настраиваются через `colors`.

---

## 5. Продвинутые приёмы

- **`DecorationBox`**  
  При глубокой кастомизации обёртки можно использовать `BasicTextField` внутри `DecorationBox`.  
- **Маскирование ввода**  
  Для номеров телефонов или дат пишите свой `VisualTransformation`, форматируя ввод по мере набора.  
- **Анимации**  
  С помощью `AnimatedVisibility` показывайте/скрывайте подсказку об ошибке с анимацией.  
- **Тестирование**  
  Используйте `Modifier.testTag("MyOutlinedField")` и в UI-тестах ищите по `hasTestTag`.