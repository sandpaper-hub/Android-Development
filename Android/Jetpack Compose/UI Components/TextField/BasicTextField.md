В Jetpack Compose `BasicTextField` — это «голый» текстовый ввод без какой-либо внешней обёртки или стилей Material. Именно на нём строятся все готовые текстовые поля (`TextField`, `OutlinedTextField`), но при этом он оставляет вам полную свободу по оформлению и поведению.

---

## Основные параметры

- **`value: String`**  
    Текущий текст.
    
- **`onValueChange: (String) → Unit`**  
    Обработчик ввода — вызывается при каждом изменении текста.
    
- **`modifier: Modifier`**  
    Позволяет задать размер, фон, отступы и прочие визуальные модификации.
    
- **`textStyle: TextStyle`**  
    Шрифт, размер, цвет текста.
    
- **`cursorBrush: Brush`**  
    Цвет или градиент курсора.
    
- **`singleLine: Boolean`** и/или **`maxLines: Int`**  
    Позволяют ограничить ввод одной строкой или несколькими.
    
- **`visualTransformation: VisualTransformation`**  
    Для маскирования (например, пароля).
    
- **`keyboardOptions`**, **`keyboardActions`**  
    Настройка клавиатуры (тип ввода, IME-действия).
    
- **`decorationBox: @Composable (innerTextField: @Composable () → Unit) → Unit`**  
    Самое важное для кастомных полей: внутри лямбды вы можете рисовать фон, рамки, плейсхолдер и вызывать `innerTextField()` там, где должен появиться сам ввод.
    

---

## Простой пример

```kotlin
@Composable
fun MyCustomTextField() {
    var text by remember { mutableStateOf("") }

    BasicTextField(
        value = text,
        onValueChange = { text = it },
        modifier = Modifier
            .fillMaxWidth()
            .height(56.dp),
        textStyle = TextStyle(fontSize = 16.sp),
        cursorBrush = SolidColor(MaterialTheme.colorScheme.primary),
        decorationBox = { innerTextField ->
            // Нарисуем рамку и плейсхолдер
            Box(
                modifier = Modifier
                    .background(
                        color = MaterialTheme.colorScheme.surface,
                        shape = RoundedCornerShape(8.dp)
                    )
                    .border(
                        width = 1.dp,
                        color = if (text.isEmpty()) Color.Gray else MaterialTheme.colorScheme.primary,
                        shape = RoundedCornerShape(8.dp)
                    )
                    .padding(horizontal = 16.dp, vertical = 12.dp)
            ) {
                if (text.isEmpty()) {
                    Text(
                        text = "Введите текст...",
                        style = TextStyle(color = Color.Gray, fontSize = 16.sp)
                    )
                }
                // Здесь отрисовывается сам вводимый текст
                innerTextField()
            }
        }
    )
}
```

**Что здесь происходит:**

1. Мы оборачиваем `innerTextField()` в `Box`, где задаём фон, рамку, отступы.
    
2. Если `text` пустой, показываем плейсхолдер.
    
3. Цвет рамки меняется в зависимости от того, пуст ли текст.
    

---

## Когда использовать `BasicTextField`

- Нужно полное управление стилем и поведением текста.
    
- Хотите сделать совершенно «нестандартное» текстовое поле (например, с иконками внутри, сложной анимацией или кастомным курсором).
    
- Не устраивают ограничения или дизайн стандартных `TextField`/`OutlinedTextField`.
    

---

## Полезные советы

- **Стилизация курсора и выделения текста** можно сделать через локальные провайдеры `LocalTextSelectionColors`.
    
- **Анимации** (появление плейсхолдера, изменение рамки) удобно делать с помощью `animate*AsState`.
    
- Если нужно просто слегка изменить стандартный `TextField`, рассмотрите `TextFieldDefaults.textFieldColors` и `TextFieldDefaults.outlinedTextFieldColors` — часто это удобнее, чем полностью кастомизировать `BasicTextField`.
    

---

`BasicTextField` — это кирпичик, с которым вы строите полностью уникальные текстовые компоненты, а готовые `TextField` на его основе дают быстрый старт в стиле Material.