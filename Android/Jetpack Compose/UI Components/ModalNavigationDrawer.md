### 🔍 Что такое `ModalNavigationDrawer` в Jetpack Compose?

`ModalNavigationDrawer` — это **выдвижная боковая панель** (drawer), которая **отображается поверх основного контента**. Она обычно вызывается при нажатии на кнопку в `TopAppBar` (иконка "гамбургер") или жестом свайпа слева направо.

📱 Это стандартный элемент Material Design, часто используется для размещения навигационного меню, профиля пользователя и других элементов второстепенной навигации.

---

### 📦 Структура компонента

```kotlin
ModalNavigationDrawer(
    drawerContent = {
        ModalDrawerSheet {
            Text("Меню 1")
            Text("Меню 2")
        }
    },
    drawerState = rememberDrawerState(DrawerValue.Closed)
) {
    // Основной экран (Main content)
}
```

---

### 🧠 Компоненты, которые используются вместе:

|Компонент|Назначение|
|---|---|
|`ModalNavigationDrawer`|Контейнер для всей структуры с боковой панелью|
|`ModalDrawerSheet`|Содержимое выдвижной панели|
|`DrawerState`|Управляет состоянием панели (открыта/закрыта)|
|`rememberDrawerState()`|Создает состояние панели|
|`TopAppBar` + `IconButton`|Запускает открытие панели при нажатии на иконку|

---

### ✅ Пример: Drawer с навигацией

```kotlin
@Composable
fun DrawerExample() {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    val coroutineScope = rememberCoroutineScope()

    ModalNavigationDrawer(
        drawerState = drawerState,
        drawerContent = {
            ModalDrawerSheet {
                Text("Главная", modifier = Modifier
                    .padding(16.dp)
                    .clickable {
                        coroutineScope.launch { drawerState.close() }
                    })
                Text("Настройки", modifier = Modifier
                    .padding(16.dp)
                    .clickable {
                        coroutineScope.launch { drawerState.close() }
                    })
            }
        }
    ) {
        Scaffold(
            topBar = {
                TopAppBar(
                    title = { Text("Пример") },
                    navigationIcon = {
                        IconButton(onClick = {
                            coroutineScope.launch { drawerState.open() }
                        }) {
                            Icon(Icons.Default.Menu, contentDescription = "Меню")
                        }
                    }
                )
            }
        ) {
            Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
                Text("Контент")
            }
        }
    }
}
```

---

### 🧭 Когда использовать?

Используйте `ModalNavigationDrawer`, когда:

- нужно показать **вторичное меню навигации**;
    
- вы хотите **сохранить чистоту интерфейса** и показывать панель только по запросу;
    
- основная навигация — в `BottomNavigation`, а drawer — для дополнительных разделов.
    

---

### 🆚 Отличие от других drawer'ов

|Вид|Особенности|
|---|---|
|`ModalNavigationDrawer`|Выдвигается поверх экрана, автоматически скрывается|
|`PermanentNavigationDrawer`|Всегда виден (например, на планшетах и десктопах)|
|`DismissibleNavigationDrawer`|Закрывается при свайпе/нажатии вне панели|

---
