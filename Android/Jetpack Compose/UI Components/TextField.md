В Jetpack Compose `TextField` — это основной элемент ввода текста по Material Design. Он отображает строку ввода, которую пользователь может редактировать, и умеет работать с состоянием, подсказками, метками, иконками и визуальными преобразованиями. Ниже — ключевые моменты и пример использования.

---

## 1. Основные разновидности

- **TextField**  
    Плоский (filled) текстовый ввод с цветным заполнением фона под текстом.
    
- **OutlinedTextField**  
    Текстовый ввод с контуром вокруг поля (outline).
    
- **BasicTextField**  
    Более низкоуровневый, без встроенных декораций — пригодится, если нужно кастомное оформление.
    

Compose предоставляет две «готовые» версии (`TextField` и `OutlinedTextField`), поверх которых лежит `BasicTextField`.

---

## 2. Важные параметры

|Параметр|Описание|
|---|---|
|`value: String`|Текущая строка текста, которую нужно отобразить.|
|`onValueChange: (String) -> Unit`|Лямбда, вызываемая при любом изменении текста.|
|`label: @Composable (() -> Unit)?`|Компонент-метка, появляющаяся над полем при фокусе (например, `Text("Имя")`).|
|`placeholder: @Composable (() -> Unit)?`|Текст-подсказка, пока поле пустое (например, `Text("Введите имя")`).|
|`leadingIcon`|Иконка слева внутри поля.|
|`trailingIcon`|Иконка справа (например, крестик для очистки).|
|`singleLine: Boolean`|Разрешить только одну строку (по умолчанию — false).|
|`maxLines: Int`|Максимальное число строк.|
|`visualTransformation: VisualTransformation`|Преобразование отображения (например, `PasswordVisualTransformation()`).|
|`keyboardOptions`|Опции клавиатуры (типы ввода, авто-коррект, заглавные).|
|`keyboardActions`|Действия при нажатии клавиш (например, `ImeAction.Done`).|
|`colors: TextFieldColors`|Цвета для разных состояний (focus, unfocus и т.п.), получают через `TextFieldDefaults.colors()`.|
|`shape: Shape`|Форма рамки/контуров.|
|`enabled: Boolean`|Включено ли поле.|

---

## 3. Пример использования

```kotlin
mport androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.padding  
import androidx.compose.material.icons.Icons  
import androidx.compose.material.icons.filled.Close  
import androidx.compose.material.icons.filled.Email  
import androidx.compose.material3.Icon  
import androidx.compose.material3.IconButton  
import androidx.compose.material3.Text  
import androidx.compose.material3.TextField  
import androidx.compose.runtime.Composable  
import androidx.compose.runtime.getValue  
import androidx.compose.runtime.mutableStateOf  
import androidx.compose.runtime.remember  
import androidx.compose.runtime.setValue  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.unit.dp  
import com.compose.jetpackcomposelearn.borderModifier  
  
@Composable  
@Suppress("functionName")  
fun SimpleTextField() {  
    Column(  
        modifier = Modifier.borderModifier,  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        var text by remember { mutableStateOf("") }  
  
        TextField(  
            value = text,  
            onValueChange = { text = it },  
            label = { Text("Email") },  
            placeholder = { Text("Input your email...") },  
            leadingIcon = {  
                Icon(Icons.Default.Email, contentDescription = null)  
            },  
            trailingIcon = {  
                if (text.isNotEmpty()) {  
                    IconButton(onClick = { text = "" }) {  
                        Icon(Icons.Default.Close, contentDescription = "Clear")  
                    }  
                }  
            },  
            singleLine = true,  
            modifier = Modifier.fillMaxWidth()  
                .padding(16.dp)  
        )    }  
}
```

- **Состояние**: текст хранится в `mutableStateOf`, привязанном к `value`/`onValueChange`.
    
- **Иконки**: `leadingIcon` и `trailingIcon` позволяют добавлять кнопки и изображения.
    
- **Label и placeholder**: `label` «выезжает» сверху, когда поле в фокусе; `placeholder` виден, пока текст пуст.

---

## 4. Продвинутые возможности

1. **VisualTransformation**  
    Позволяет скрывать или форматировать ввод сразу по мере набора. Часто используется для паролей:
    
    ```kotlin
    visualTransformation = PasswordVisualTransformation()
    ```
    
2. **DecorationBox**  
    Если вам нужно полностью кастомизировать обёртку `BasicTextField`, вы можете использовать параметр `decorationBox`.
    
3. **InputMasking**  
    Через сторонние библиотеки или кастомные `VisualTransformation` можно добавлять маски (например, для даты или номера телефона).
    
4. **Error State**  
    Для валидации можно определить булеву `hasError` и в `colors` указать красный цвет:
    
    ```kotlin
    colors = TextFieldDefaults.outlinedTextFieldColors(
        focusedBorderColor = if (hasError) Color.Red else MaterialTheme.colorScheme.primary
    )
    ```
    
5. **Поддержка мультиязыка и RTL**  
    `TextField` корректно отражает направления текста и позволяет вводить любые скрипты.
    

---

`TextField` в Compose — гибкий и мощный инструмент для текстового ввода. Он сразу покрывает большинство сценариев Material Design, но при необходимости позволяет глубокую кастомизацию через `BasicTextField`, `decorationBox` и `VisualTransformation`. Используйте его вместе с `OutlinedTextField` или `BasicTextField`, чтобы покрыть все требования к форме и UX вашего приложения.