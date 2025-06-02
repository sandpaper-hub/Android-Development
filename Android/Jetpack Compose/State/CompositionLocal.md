**`CompositionLocal`** (ранее известный как «Ambient») — это механизм в Jetpack Compose, который позволяет **передавать значения «сквозь» иерархию композиций без необходимости прокидывать их явными параметрами через все вложенные функции**. Проще говоря, это способ создать иерархический «контекст», доступный любому `@Composable`, находящемуся ниже в дереве, аналогично тому, как в классическом Android есть `Context` или как в React есть «Context API».

## Зачем это нужно

1. **Избежать «прокидывания» параметров (prop drilling)**  
    Если у вас есть какая-то глобальная/широко используемая информация (например, текущая тема, локализация, навигационный контроллер, настройки шрифтов, единицы измерения и т.д.), то вместо того чтобы передавать её **через каждый вызов** `@Composable` в глубине дерева, вы объявляете `CompositionLocal` и «поднимаете» туда значение. Дочерние composable могут **извлекать** его, не получая его явно в параметрах.
    
2. **Глобальные настройки (тема, междумязыковой словарь, единицы измерения)**  
    В Compose есть уже несколько предопределённых `CompositionLocal`:
    
    - `LocalContext` — система передаёт текущий Android `Context`.
        
    - `LocalDensity` — хранит текущие плотность (`density`) и конвертеры dp↔px.
        
    - `LocalLayoutDirection` — направление макета (LTR/RTL).
        
    - `LocalConfiguration` — доступ к `Resources.Configuration` (разрешение экрана, ориентация и т.п.).
        
    - `LocalTextStyle` — базовый стиль текста, указываемый в `Text { … }`.
        
    - `LocalContentColor` и `LocalContentAlpha` — цвет/альфа канала, унаследованные от родительского `Surface`/`Theme`.
        
    - `LocalLayoutDirection` — направление (например, для RTL-языков).  
        Перечисленные локалы удовлетворяют потребности многих базовых библиотек.
        
3. **Изменение поведения «нижестоящих» компонентов**  
    С помощью `CompositionLocalProvider` можно временно **заменить** значение локала в некоторой ветке, и все `@Composable` внутри неё получат уже «локально переопределённое» значение.

## Как это работает: API

### 1. Объявление собственного `CompositionLocal`
```kotlin
// Типизированный локал, храним значение типа T
val LocalMyData = compositionLocalOf<MyDataType> {
    error("LocalMyData не был установлен")
}
```

- `compositionLocalOf(defaultFactory: () -> T)` создаёт «несменяемый» (immutable) локал, у которого **нет** встроенного «запасного» или «дефолтного» значения, а бросает ошибку, если вы его не установили сверху через `CompositionLocalProvider`.
    
- Если вы хотите указать **дефолтное значение**, используйте:
```kotlin
val LocalMyData = compositionLocalOf { MyDataType(…дефолт…) }
```

Тогда дочерние компоненты всегда получат это дефолтное, если вы не переопределите его.

Есть ещё `staticCompositionLocalOf { … }`, который создаёт локал, не отслеживающий изменения внутри лямбды по умолчанию и более «статичный». Но, как правило, хватает `compositionLocalOf`.

### 2. Установка (Provision) значения

Чтобы передать (или переопределить) значение в дочерние composable, оборачиваем нужную ветку в `CompositionLocalProvider`:

```kotlin
@Composable
fun MyApp() {
    val data = remember { MyDataType(/* какие-то поля */) }

    CompositionLocalProvider(
        LocalMyData provides data
    ) {
        // Всё, что здесь находится, «видит» LocalMyData = data
        ScreenA()
    }
}
```

- `LocalMyData provides data` — это синтаксический сахар:

```kotlin
LocalMyData.provides(data)
```

- Внутри блока `CompositionLocalProvider` все **дочерние** `@Composable` смогут вызвать `LocalMyData.current` и получить `data`.

Если нужно переопределить локал сразу несколькими значениями, передаём их через запятую:

```kotlin
CompositionLocalProvider(
    LocalMyData provides data1,
    LocalAnotherData provides data2
) {
    ChildComposable()
}
```

### 3. Чтение (Consumption) значения

Внутри любой `@Composable`, находящейся в ветке, где вы произвели `CompositionLocalProvider`, можно получать текущее значение:

```kotlin
@Composable
fun ScreenA() {
    // считываем: если вышли за пределы блока, где провайдили, то будет дефолт/ошибка
    val data: MyDataType = LocalMyData.current

    Text("Значение: ${data.someProperty}")
}
```

- **Важно**: при обращении к `LocalMyData.current` _compose_ также будет подписан на изменения этого локала. Если вы где-то вверху передадите другой объект (через переименование `provides`), Compose заметит это и «рекомпозирует» все места, где `.current` использовался.

#### Пример простого использования
```kotlin
val LocalFocusColor = compositionLocalOf { Color.Gray }  
  
@Composable  
@Suppress("FunctionName")  
fun CompositionLocalFun() {  
    val preferredColor = remember { Color(0xFF4CAF50) }  
    CompositionLocalProvider(  
        LocalFocusColor provides preferredColor  
    ) {  
        NestedFun()  
    }  
}  
  
@Composable  
@Suppress("FunctionName")  
fun NestedFun() {  
    Column {  
        EditableField("Имя")  
        EditableField("Фамилия")  
    }  
}  
  
@Composable  
@Suppress("FunctionName")  
fun EditableField(label: String) {  
    val focusColor = LocalFocusColor.current  
    var text by rememberSaveable { mutableStateOf("") }  
    TextField(  
        value = text,  
        onValueChange = { text = it },  
        modifier = Modifier  
            .fillMaxWidth()  
            .padding(vertical = 8.dp),  
        colors = TextFieldDefaults.colors(  
            focusedIndicatorColor = focusColor,  
            unfocusedIndicatorColor = focusColor.copy(alpha = 0.5f)  
        ),        label = { Text(label) }  
    )  
}
```

## Предопределённые локалы и их примеры

1. **`LocalContext`**
    
    - Тип: `ProvidableCompositionLocal<Context>`.
        
    - Заводится автоматически (Compose «зашивает» его в самый верх).
        
    - Используется, чтобы внутри `@Composable` получить нативный `android.content.Context`:
```kotlin
val context = LocalContext.current
Toast.makeText(context, "Hello", Toast.LENGTH_SHORT).show()
```

2. **`LocalDensity`**

	- Тип: `ProvidableCompositionLocal<Density>`.
    
	- Содержит `density`, `fontScale`, и методы `Dp.toPx()`, `Px.toDp()`.
    
	- Позволяет писать конвертеры обратно-вперед внутри composable:

```kotlin
val density = LocalDensity.current
val px = with(density) { 16.dp.toPx() }
```

3. **`LocalLayoutDirection`**

- Тип: `ProvidableCompositionLocal<LayoutDirection>`.
    
- Может быть `LayoutDirection.Ltr` или `LayoutDirection.Rtl`.
    
- Используется внутри `Row`/`Column` для правильного выравнивания в RTL-режиме.

4. **`LocalConfiguration`**

- Тип: `ProvidableCompositionLocal<Configuration>`.
    
- Даёт доступ к `Resources.getConfiguration()`: ширина экрана, ориентация, locale и т. д.
    
- Если ориентация меняется, Compose «услышит» это и пересоздаст `Configuration`.

5. **`LocalTextStyle`**

- Тип: `ProvidableCompositionLocal<TextStyle>`.
    
- Используется внутри `Text`-компонентов для наследования базового стиля.
    
- Например, если вы хотите, чтобы все `Text` внутри некоторой карточки использовали кастомный `TextStyle`, достаточно обернуть их в:
```kotlin
CompositionLocalProvider(
    LocalTextStyle provides TextStyle(fontSize = 24.sp, color = Color.Magenta)
) {
    Column {
        Text("Первый")
        Text("Второй")
    }
}
```
- Оба `Text` унаследуют `fontSize = 24.sp, color = Magenta`, если не передали свой стиль.

6. **`LocalContentColor`** / **`LocalContentAlpha`**

- Используется внутри разных компонентов Material, чтобы определить, каким цветом отображать «контент» (иконки, тексты) по умолчанию.
    
- Обычно задаётся при создании `Surface`/`Card`. Например:

```kotlin
Surface(color = Color.Blue) {
    // По умолчанию LocalContentColor.current == Color.White
    Icon(Icons.Default.Star, contentDescription = null)  
    Text("Hello")
}
```

## Когда стоит создавать собственные `CompositionLocal`

- **Глобальная тема**. Если вы не хотите передавать `Theme` или `Typography` как параметр, можно сделать свой локал, где хранятся цвета, отступы, шрифты.
    
- **Синглтон́–данные**, доступные нескольким вложенным `@Composable` без явного параметра.
    
- **Навигация**. Внутри некоторых библиотек навигации (`Navigation Compose`) используют `CompositionLocal` для передачи `NavController` вниз по дереву, чтобы можно было вызвать `navController.navigate("route")`, не принимая `NavController` параметром.

## Важные нюансы

1. **Локалы не предназначены для «мелкого» состояния UI**.  
    Если вам нужно хранить локальное состояние (например, «какая панель сейчас свёрнута»), лучше использовать `remember { mutableStateOf(...) }` внутри компонента. `CompositionLocal` — это всё же про **«контекстные»** данные, которые меняются не так часто.
    
2. **Изменение значения провайдера вызывает «рекомпозицию» всех потребителей**.  
    Если вы часто меняете значение `LocalMyData`, все `@Composable`, где вы вызываете `LocalMyData.current`, будут перерисовываться. Следите за тем, чтобы это не приводило к перерисовкам слишком больших участков.
    
3. **Порядок вложенности важен**.  
    Если внутри блока переопределять локал:

```kotlin
CompositionLocalProvider(LocalThemeColor provides Color.Red) {
    SomeComposable()
    CompositionLocalProvider(LocalThemeColor provides Color.Blue) {
        ChildComposable()
    }
}
```
 
 то внутри `SomeComposable()` будет **красный**, а внутри `ChildComposable()` будет **синий**. После выхода из второго блока дети вновь видят красный.

4. **Типобезопасность**.  
    Компилятор гарантирует, что вы извлекаете локал того же типа, что и объявили. Нельзя получить `LocalFoo.current` как `Bar`, если при объявлении было `compositionLocalOf<Foo>`.