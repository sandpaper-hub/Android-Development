

FloatingActionButton (FAB) — это круглая плавающая кнопка, используемая для самых важных действий на экране. Она «плавает» над остальным контентом и привлекает внимание пользователя к ключевому действию.

---

## 1. Основная концепция

- **Фокус на одно действие**. FAB предназначена для одного «главного» действия (например, добавление новой записи, создание сообщения и т. п.).  
- **Позиционирование**. Обычно располагается в правом нижнем углу экрана (или слева для RTL), но может быть закреплена в `Scaffold` (Compose) или `CoordinatorLayout` (View).  
- **Виден поверх контента**. Часто имеет тень (elevation), чтобы «парить» над списками и картинками.

---
## 2. FloatingActionButton в Jetpack Compose

В Compose FAB встроена в `Scaffold`:

```kotlin
@Composable
fun MyScreen() {
    Scaffold(
        floatingActionButton = {
            FloatingActionButton(
                onClick = { /* главное действие */ },
                contentColor = Color.White,                // цвет иконки
                backgroundColor = MaterialTheme.colors.primary // цвет фона
            ) {
                Icon(
                    imageVector = Icons.Default.Add,
                    contentDescription = "Добавить"
                )
            }
        },
        floatingActionButtonPosition = FabPosition.End,    // End или Center
        isFloatingActionButtonDocked = false               // «встык» к BottomAppBar?
    ) { padding ->
        // Остальной контент экрана, не забывайте учитывать padding
        Box(Modifier.padding(padding)) { /*...*/ }
    }
}
```

- `FloatingActionButtonPosition`: `End` (право) или `Center` (центр).  
- `isFloatingActionButtonDocked`: если используете `BottomAppBar` и хотите «утопить» кнопку в его вырезе.  

---

## 3. Extended FAB

Расширенный FAB — содержит иконку и текст:

```kotlin
@Composable
fun MyExtendedFab() {
    ExtendedFloatingActionButton(
        text = { Text("Создать") },
        icon = { Icon(Icons.Default.Create, "Создать") },
        onClick = { /* действие */ }
    )
}
```

- Автоматически подстраивает размер под текст.  
- Хорош для экранов, где нужно пояснить действие.

---

## 4. Настройка и стилизация

```kotlin
FloatingActionButton(
    onClick = { /*...*/ },
    modifier = Modifier
        .size(64.dp)               // кастомный диаметр
        .padding(16.dp),
    backgroundColor = Color.Magenta, 
    contentColor = Color.Yellow,
    elevation = FloatingActionButtonDefaults.elevation(
        defaultElevation = 8.dp,
        pressedElevation = 12.dp
    )
) {
    Icon(
        imageVector = Icons.Default.Share,
        contentDescription = "Поделиться",
        modifier = Modifier.size(32.dp)  // размер иконки внутри
    )
}
```

- `modifier.size()` — диаметр кнопки.  
- `elevation` — задаёт тень в обычном и нажатом состояниях.  
- Цвета можно брать из темы (`MaterialTheme.colors`) или задавать напрямую.

---

## 5. Best practices

1. **Единая точка действия.** Используйте не больше одной FAB на экране.  
2. **Touch target.** Диаметр не менее 48 dp; внутренняя иконка — около 24–32 dp.  
3. **Accessibility.** Обязательно указывайте `contentDescription`.  
4. **Позиционирование.** Следите, чтобы FAB не закрывала важный контент и не конфликтовала с системными элементами (навигационная панель, жесты).  
5. **Темing.** Поддерживайте светлую и тёмную тему, берите цвета из `MaterialTheme`.  
6. **State hoisting.** Логику действия (например, навигацию или изменение ViewModel) выносите из Composable с FAB.

---

## 6. Когда использовать

- Создание новой сущности (запись, сообщение, фото).  
- Выполнение ключевого действия, доступного в любой точке экрана.  
- Если действие не центральное или их несколько — предпочтительнее использовать обычные кнопки или меню.

---

Если нужно разобрать конкретный случай интеграции FAB с `BottomAppBar`, поведением при скролле (скрывать/показывать) или анимациями — дайте знать!