В Jetpack Compose оба бара — верхний и нижний — интегрируются через `Scaffold` и гарантируют единообразный Material-UX. 

---

## 1. TopAppBar

### 1.1. Что это такое

Компонент, который располагается в верхней части экрана, содержит:

- **Navigation icon** (чаще «гамбургер» или «стрелка назад»)
    
- **Title** (текст заголовка)
    
- **Actions** (иконки действий справа)
    

### 1.2. Пример

```kotlin
@OptIn(ExperimentalMaterial3Api::class)  
@Composable  
@Suppress("FunctionName")  
fun TopAppBarExample(  
    title: String,  
    onNavigationClick: () -> Unit,  
    actions: @Composable RowScope.() -> Unit = {}  
) {  
    TopAppBar(  
        title = { Text(text = title) },  
        navigationIcon = {  
            IconButton(onClick = onNavigationClick) {  
                Icon(  
                    Icons.AutoMirrored.Filled.ArrowBack,  
                    contentDescription = "Back"  
                )  
            }  
        },  
        actions = actions  
    )  
}
```

Использование:

```kotlin
Scaffold(
  topBar = {
    MyTopAppBar(
      title = "Список задач",
      onNavigationClick = { /* навигация вверх */ }
    ) {
      IconButton(onClick = { /* поиск */ }) {
        Icon(Icons.Default.Search, "Поиск")
      }
      IconButton(onClick = { /* фильтр */ }) {
        Icon(Icons.Default.FilterList, "Фильтр")
      }
    }
  }
) { padding ->
  // контент с учётом padding
}
```

---

## 2. BottomAppBar

### 2.1. Что это такое

Нижний бар, обычно содержит:

- **Навигационные элементы** (иконки или текст)
    
- **Дополнительные действия**
    
- Может «утопаться» вокруг FAB (при `isFloatingActionButtonDocked = true`)
    

### 2.2. Пример

```kotlin
@Composable  
@Suppress("FunctionName")  
fun BottomAppBarExample(  
    onHomeClick: () -> Unit,  
    onFavoriteClick: () -> Unit,  
    onSettingsClick: () -> Unit  
) {  
    BottomAppBar(  
        tonalElevation = 4.dp,  
        windowInsets = WindowInsets.navigationBars.only(WindowInsetsSides.Horizontal + WindowInsetsSides.Bottom)  
    ) {  
        Spacer(Modifier.weight(1f, true))  
        IconButton(onClick = onHomeClick) {  
            Icon(Icons.Default.Home, contentDescription = "Home")  
        }  
        Spacer(Modifier.weight(1f, true))  
        IconButton(onClick = onFavoriteClick) {  
            Icon(Icons.Default.Favorite, contentDescription = "Favorite")  
        }  
        Spacer(Modifier.weight(1f, true))  
        IconButton(onClick = onSettingsClick) {  
            Icon(Icons.Default.Settings, contentDescription = "Settings")  
        }  
        Spacer(Modifier.weight(1f, true))  
    }  
}
```

Использование в Scaffold:

```kotlin
Scaffold(
  floatingActionButton = { /* ваш FAB */ },
  isFloatingActionButtonDocked = true,
  floatingActionButtonPosition = FabPosition.Center,
  bottomBar = {
    MyBottomAppBar(
      onHomeClick = { /*…*/ },
      onProfileClick = { /*…*/ }
    )
  }
) { padding ->
  // основной контент
}
```

---

## 3. Лучшие практики

1. **State hoisting**: все коллбэки (`onNavigationClick`, `onHomeClick` и др.) выносите в родительский слой.
    
2. **Учёт отступов**: всегда передавайте `paddingValues` из `Scaffold` в контент, чтобы контент не «попадал» под бары.
    
3. **Accessibility**: каждому `IconButton` задавайте понятный `contentDescription`.
    
4. **Теминг**: цвета берите из `MaterialTheme`, чтобы поддерживать светлую и тёмную темы.
    
5. **Вырез под FAB**: если используете плавающую кнопку «встык» к нижнему бару, укажите `cutoutShape` и `isFloatingActionButtonDocked = true`.
    

---

Таким образом, можно выносить `TopAppBar` и `BottomAppBar` в отдельные `@Composable`-функции, настраивать их через параметры и легко переиспользовать в разных экранах.