## `Text` — Отображение Текста

`Text` — это фундаментальный компонент для отображения статического или динамического текста.

### Использование:
```kotlin
@Composable
fun MyTextExamples() {
    Text(text = "Привет, Compose!")
    Text("Это еще один текст.") // Можно опустить 'text =' для первого параметра
}
```

### Основные параметры`Modifier`

`Text` имеет множество параметров для детальной настройки внешнего вида текста. Помимо обязательного `text`, вот наиболее важные и часто используемые:

- **`text: String`** (обязательный): Строка текста, которую нужно отобразить.
- **`modifier: Modifier`**: Модификатор, который позволяет настроить внешний вид и поведение компонента `Text` (размер, отступы, фон, клики и т.д.)

```kotlin
Text(
    text = "Текст с отступом и фоном",
    modifier = Modifier
        .padding(16.dp)
        .background(Color.LightGray)
)
```

- **`color: Color`**: Устанавливает цвет текста.
```kotlin
Text(text = "Красный текст", color = Color.Red)
```

- **`fontSize: TextUnit`**: Размер шрифта. Используйте `sp` (scale-independent pixels) для размера шрифта.
```kotlin
Text(text = "Большой текст", fontSize = 24.sp)
```

- **`fontStyle: FontStyle`**: Стиль шрифта (нормальный, курсив).
```kotlin
Text(text = "Курсив", fontStyle = FontStyle.Italic)
```

- **`fontWeight: FontWeight`**: Толщина шрифта (обычный, жирный, полужирный и т.д.).
```kotlin
Text(text = "Жирный текст", fontWeight = FontWeight.Bold)
```

- **`fontFamily: FontFamily?`**: Семейство шрифтов (например, `FontFamily.Serif`, `FontFamily.SansSerif`, или загруженные пользовательские шрифты).
```kotlin
Text(text = "Шрифт Serif", fontFamily = FontFamily.Serif)
```

- **`letterSpacing: TextUnit`**: Расстояние между символами в тексте.
```kotlin
Text(text = "Р А З Р Я Д К А", letterSpacing = 4.sp)
```

- **`textDecoration: TextDecoration?`**: Декорация текста (подчеркивание, зачеркивание).
```
kotlin
Text(text = "Подчеркнутый текст", textDecoration = TextDecoration.Underline)
Text(text = "Зачеркнутый текст", textDecoration = TextDecoration.LineThrough)
```

- **`textAlign: TextAlign?`**: Горизонтальное выравнивание текста внутри его компон
```kotlin
// В Column или Box с fillMaxWidth() для демонстрации
Column(modifier = Modifier.fillMaxWidth()) {
    Text(text = "Выравнивание по центру", textAlign = TextAlign.Center, modifier = Modifier.fillMaxWidth())
    Text(text = "Выравнивание по левому краю", textAlign = TextAlign.Start, modifier = Modifier.fillMaxWidth())
    Text(text = "Выравнивание по правому краю", textAlign = TextAlign.End, modifier = Modifier.fillMaxWidth())
}
```

- **`lineHeight: TextUnit`**: Высота каждой строки текста.
```kotlin
Text(text = "Многострочный\nтекст\nс измененной высотой строки", lineHeight = 30.sp)
```

- **`overflow: TextOverflow`**: Как обрабатывать текст, который не помещается в заданные границы.

- `TextOverflow.Clip`: Обрезает текст.
- `TextOverflow.Ellipsis`: Добавляет многоточие (`...`) в конце.
```kotlin
Text(
    text = "Очень длинный текст, который должен быть обрезан или показан с многоточием...",
    maxLines = 1,
    overflow = TextOverflow.Ellipsis,
    modifier = Modifier.width(150.dp) // Ограничиваем ширину для демонстрации
)
```

- **`softWrap: Boolean`**: Разрешает ли перенос слов. По умолчанию `true`. Если `false`, текст будет продолжаться одной строкой.
- **`maxLines: Int`**: Максимальное количество строк, которые можно отобразить. Если текст превышает это количество, он будет обрезан или показан с многоточием в зависимости от `overflow`.
- **`minLines: Int`**: Минимальное количество строк. Если текст занимает меньше, будут добавлены пустые строки до этого значения. (Полезно для сохранения стабильной высоты, даже если контента мало).
- **`style: TextStyle`**: Позволяет применить целый набор стилей, объединяя множество параметров в одном объекте `TextStyle`. Это очень удобно для создания и применения единого стиля к нескольким `Text` компонентам или для использования стилей из темы Material Design.
```kotlin
import androidx.compose.material3.MaterialTheme
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight

@Composable
fun StyledTextWithTheme() {
    // Использование предопределенных стилей из MaterialTheme
    Text(
        text = "Заголовок из темы",
        style = MaterialTheme.typography.headlineLarge
    )
    Text(
        text = "Текст тела из темы",
        style = MaterialTheme.typography.bodyLarge
    )

    // Создание собственного TextStyle
    val customTextStyle = TextStyle(
        color = Color.Blue,
        fontSize = 20.sp,
        fontWeight = FontWeight.SemiBold,
        letterSpacing = 1.sp
    )
    Text(
        text = "Мой собственный стиль",
        style = customTextStyle
    )
}
```


### `AnnotatedString` — Разнообразное Форматирование в Одном `Text` Компоненте

Одной из самых мощных возможностей `Text` является поддержка `AnnotatedString`. Это позволяет применять различные стили (цвет, шрифт, вес и т.д.), а также добавлять кликабельные диапазоны (спаны) к разным частям одного текста.

Для создания `AnnotatedString` используется функция `buildAnnotatedString`.

- **Пример с разными стилями:**
```kotlin
import androidx.compose.material3.Text
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.text.TextDecoration

@Composable
fun AnnotatedTextExample() {
    Text(
        buildAnnotatedString {
            withStyle(style = SpanStyle(color = Color.Blue, fontWeight = FontWeight.Bold)) {
                append("Это синий, жирный текст. ")
            }
            append("Это обычный текст. ")
            withStyle(style = SpanStyle(textDecoration = TextDecoration.Underline, fontSize = 18.sp)) {
                append("Это подчеркнутый текст.")
            }
        }
    )
}
```


- **Пример с кликабельным текстом**
```kotlin
import android.content.Intent
import android.net.Uri
import android.util.Log
import androidx.compose.foundation.gestures.detectTapGestures
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.text.BasicText
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalUriHandler
import androidx.compose.ui.text.SpanStyle
import androidx.compose.ui.text.TextLayoutResult
import androidx.compose.ui.text.buildAnnotatedString
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.text.withStyle
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun ClickableTextExample() {
    val context = LocalContext.current
    val uriHandler = LocalUriHandler.current

    // Храним результат layout-а текста, чтобы знать соответствие координат ↔️ offset в строке
    var textLayoutResult by remember { mutableStateOf<TextLayoutResult?>(null) }

    val annotatedText = buildAnnotatedString {
        append("Посетите ")
        pushStringAnnotation(tag = "URL", annotation = "https://www.google.com")
        withStyle(style = SpanStyle(color = MaterialTheme.colorScheme.primary, textDecoration = TextDecoration.Underline)) {
            append("Google")
        }
        pop()

        append(" или прочтите ")
        pushStringAnnotation(tag = "TERMS", annotation = "terms_and_conditions")
        withStyle(style = SpanStyle(color = MaterialTheme.colorScheme.primary, textDecoration = TextDecoration.Underline)) {
            append("Условия использования")
        }
        pop()

        append(".\nИли нажмите на ")
        pushStringAnnotation(tag = "ACTION", annotation = "show_message")
        withStyle(style = SpanStyle(color = MaterialTheme.colorScheme.secondary, fontWeight = FontWeight.Bold)) {
            append("специальный текст")
        }
        pop()
    }

    Text(
        text = annotatedText,
        style = MaterialTheme.typography.bodyLarge.copy(fontSize = 16.sp),
        modifier = Modifier
            .padding(16.dp)
            // Отслеживаем клики и получаем координаты нажатия
            .pointerInput(Unit) {
                detectTapGestures { offset: Offset ->
                    val layoutResult = textLayoutResult ?: return@detectTapGestures
                    // находим позицию символа
                    val pos = layoutResult.getOffsetForPosition(offset)

                    // Обрабатываем каждую аннотацию
                    annotatedText.getStringAnnotations("URL", pos, pos)
                        .firstOrNull()?.let { ann ->
                            Log.d("ClickableText", "Clicked URL: ${ann.item}")
                            uriHandler.openUri(ann.item)
                        }
                    annotatedText.getStringAnnotations("TERMS", pos, pos)
                        .firstOrNull()?.let { ann ->
                            Log.d("ClickableText", "Clicked Terms: ${ann.item}")
                            // Например, показать Dialog с условиями
                        }
                    annotatedText.getStringAnnotations("ACTION", pos, pos)
                        .firstOrNull()?.let { ann ->
                            Log.d("ClickableText", "Clicked Action: ${ann.item}")
                            // Выполнить какое-то действие
                        }
                }
            },
        // Сохраняем layout результата для последующего расчёта offset
        onTextLayout = { textLayoutResult = it },
        maxLines = 3,
        overflow = TextOverflow.Ellipsis
    )
}

```

- **Построение аннотированной строки (`AnnotatedString`)**
    
    - Используем `buildAnnotatedString { … }`
        
    - Метим фрагменты тегами `URL`, `TERMS`, `ACTION` и задаём им стили (`SpanStyle` с подчёркиванием, цветом, жирностью).
        
- **Компонент `Text` из Material 3**
    
    - Выводим `annotatedText` с базовым стилем (`bodyLarge`, размер 16 sp).
        
    - Ограничиваем `maxLines = 3` и `overflow = TextOverflow.Ellipsis`.
        
- **Сохранение результата layout-а**
    
    - В параметре `onTextLayout` сохраняем переданный `TextLayoutResult` в `remember { mutableStateOf(...) }`.
        
- **Обработка тапов через `pointerInput`**
    
    - Подключаем модификатор `.pointerInput { detectTapGestures { offset -> … } }`.
        
    - Получаем координаты касания `offset: Offset`.
        
- **Вычисление позиции символа**
    
    - С помощью `textLayoutResult.getOffsetForPosition(offset)` находим индекс символа в строке.
        
- **Чтение аннотаций по позиции**
    
    - Для каждого тега (`"URL"`, `"TERMS"`, `"ACTION"`) вызываем
	- В блоке `let` выполняем нужное действие:

	- `uriHandler.openUri(ann.item)` для открытия ссылки.
    
	- Логирование через `Log.d`.
    
	- Другие кастомные реакции (диалог, навигация, показ сообщения).

### Локальный Стилист и Тематика (Theming)

Вместо того чтобы вручную задавать каждый параметр стиля для каждого `Text` компонента, Jetpack Compose активно использует систему тематизации Material Design.

- **`MaterialTheme.typography`**: Содержит набор предопределенных `TextStyle` объектов (например, `headlineLarge`, `bodyMedium`, `labelSmall`), которые соответствуют рекомендациям Material Design.
```kotlin
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text

@Composable
fun ThemedTextExamples() {
    Column {
        Text(text = "Заголовок 1", style = MaterialTheme.typography.headlineLarge)
        Text(text = "Заголовок 2", style = MaterialTheme.typography.headlineMedium)
        Text(text = "Тело текста", style = MaterialTheme.typography.bodyLarge)
        Text(text = "Маленькая подпись", style = MaterialTheme.typography.labelSmall)
    }
}
```
Использование тематики гарантирует единообразие UI и облегчает изменение общего стиля приложения.

- **`LocalTextStyle`**: Компоненты Compose наследуют `TextStyle` от своих родителей через `CompositionLocal`. Вы можете временно переопределить этот стиль для поддерева UI с помощью `CompositionLocalProvider`.
```kotlin
import androidx.compose.material3.LocalTextStyle
import androidx.compose.material3.Text
import androidx.compose.runtime.CompositionLocalProvider
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.unit.sp
import androidx.compose.ui.graphics.Color

@Composable
fun CustomTextScope() {
    Column {
        Text("Это текст по умолчанию")
        CompositionLocalProvider(LocalTextStyle provides TextStyle(fontSize = 20.sp, color = Color.Green)) {
            Text("Этот текст внутри провайдера, он зеленый и большой.")
            Text("И этот текст тоже зеленый и большой.")
            // Вложенный провайдер
            CompositionLocalProvider(LocalTextStyle provides TextStyle(color = Color.Magenta)) {
                Text("А этот текст внутри вложенного провайдера, он пурпурный.")
            }
            Text("Этот текст снова зеленый и большой.")
        }
        Text("И этот текст снова по умолчанию.")
    }
}
```

### Производительность и Рекомпозиция

- **Статический текст:** Если текст не меняется, Compose не будет его рекомпозировать.
- **Динамический текст:** Если текст берется из `State` или другого изменяемого источника, `Text` будет рекомпозирован при изменении значения, но только если это необходимо. Compose оптимизирует это, обновляя только те части UI, которые изменились.

### Важные зависимости
Для работы с `Text` и его стилями убедитесь, что у вас есть следующие зависимости в `build.gradle (Module: app)`:
```kotlin
implementation "androidx.compose.ui:ui"
implementation "androidx.compose.material3:material3" // Для MaterialTheme.typography
```
