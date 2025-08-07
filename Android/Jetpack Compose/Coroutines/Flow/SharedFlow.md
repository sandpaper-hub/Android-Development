`SharedFlow` — это мощный инструмент из библиотеки **Kotlin Coroutines**, предназначенный для **рассылки событий** нескольким подписчикам. Он похож на `StateFlow`, но не хранит состояние, а передаёт **одноразовые события**, такие как:

- показать Snackbar
    
- навигация на другой экран
    
- отображение ошибки
    
- лог событий
    

---

## 🧩 Что такое SharedFlow?

`SharedFlow` — это **горячий поток**, который **всегда активен** и может иметь **множество подписчиков одновременно**. Он похож на `Broadcast`.

```kotlin
val sharedFlow = MutableSharedFlow<String>()
```

---

## 📌 Отличие от StateFlow

||**SharedFlow**|**StateFlow**|
|---|---|---|
|Хранит последнее значение?|❌ (если не настраивать)|✅|
|Подходит для UI состояния|❌|✅|
|Подходит для событий|✅|❌|
|Требует начального значения|❌|✅|
|Работает без подписчика|✅|✅|

---

## 📦 Пример: Snackbar через SharedFlow

### 🔹 ViewModel:

```kotlin
class MyViewModel : ViewModel() {
    private val _eventFlow = MutableSharedFlow<String>()
    val eventFlow: SharedFlow<String> = _eventFlow

    fun onButtonClicked() {
        viewModelScope.launch {
            _eventFlow.emit("Кнопка нажата!")
        }
    }
}
```

### 🔹 UI (Jetpack Compose):

```kotlin
@Composable
fun MyScreen(viewModel: MyViewModel) {
    val snackbarHostState = remember { SnackbarHostState() }

    // Слушаем SharedFlow внутри LaunchedEffect
    LaunchedEffect(Unit) {
        viewModel.eventFlow.collect { message ->
            snackbarHostState.showSnackbar(message)
        }
    }

    Scaffold(snackbarHost = { SnackbarHost(snackbarHostState) }) {
        Button(onClick = { viewModel.onButtonClicked() }) {
            Text("Нажми меня")
        }
    }
}
```

---

## ⚙️ Настройка SharedFlow

```kotlin
val flow = MutableSharedFlow<String>(
    replay = 1,           // сколько последних значений хранить
    extraBufferCapacity = 1, // буфер для переполнения
    onBufferOverflow = BufferOverflow.DROP_OLDEST // или SUSPEND / DROP_LATEST
)
```

### Значение `replay`

- `0` — событие увидит только **текущий** подписчик
    
- `1+` — новые подписчики получат **несколько прошлых событий**
    

---

## 🎯 Когда использовать SharedFlow

|Сценарий|SharedFlow?|
|---|---|
|Навигация на другой экран|✅|
|Показ Snackbar / Toast|✅|
|UI-состояние (например, список пользователей)|❌ (лучше `StateFlow`)|
|Одноразовые ошибки из репозитория|✅|

---

## 🛑 Важно

- `SharedFlow` не перезапускается — он всегда активен
    
- Если у тебя нет подписчиков, `emit()` может зависнуть, если буфера нет — используй `tryEmit()` или `extraBufferCapacity`
    
- **Не подходит для хранения UI-состояния** → для этого `StateFlow`
    

---

## 📎 Резюме

|Характеристика|SharedFlow|
|---|---|
|Тип|Горячий поток событий|
|Подходит для|Snackbar, навигация, алерты|
|Имеет значение по умолчанию|❌|
|Пример в Compose|Через `LaunchedEffect + collect`|
|Настройки|`replay`, `buffer`, `overflow`|
