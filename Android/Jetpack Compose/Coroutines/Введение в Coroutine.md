Корутины и асинхронность — это ключевые инструменты в Jetpack Compose (и вообще в Android), которые позволяют выполнять долгие или неблокирующие операции (например, сетевые запросы или работу с БД) без блокировки главного потока (UI-потока). Jetpack Compose полностью поддерживает **Kotlin Coroutines**, что делает их основным способом обработки асинхронного кода в современном Android.

---

## **Почему корутины важны в Jetpack Compose?**

Jetpack Compose построен на реактивной парадигме и использует **`recomposition`** для обновления UI. Если выполнить долгую операцию в главном потоке, UI "заморозится".  
Корутины позволяют:

- запускать задачи в фоновом потоке;
    
- безопасно обновлять UI при завершении задачи;
    
- использовать упрощённый синтаксис `suspend` вместо коллбеков.
    

---

## **Основные инструменты для корутин в Jetpack Compose**

### 1. **`rememberCoroutineScope()`**

- Позволяет запускать корутины, привязанные к жизненному циклу `Composable`.
    
- Если `Composable` удаляется, все корутины, запущенные в этом `scope`, будут отменены.
    

**Пример:**

```kotlin
@Composable
fun CoroutineButton() {
    val scope = rememberCoroutineScope()

    Button(onClick = {
        scope.launch {
            delay(2000)
            println("Button clicked after 2 seconds!")
        }
    }) {
        Text("Click Me")
    }
}
```

---

### 2. **`LaunchedEffect()`**

- Запускает корутину при **входе в Composable** или при изменении **key-параметров**.
    
- Используется для выполнения побочных эффектов (например, загрузки данных).
    

**Пример:**

```kotlin
@Composable
fun DataLoader() {
    var text by remember { mutableStateOf("Loading...") }

    LaunchedEffect(Unit) {
        delay(2000)
        text = "Data Loaded!"
    }

    Text(text)
}
```

> Здесь корутина запускается один раз при первом вызове Composable.

---

### 3. **`produceState()`**

- Создаёт состояние (`State<T>`) из корутины.
    
- Удобно для асинхронной загрузки данных, чтобы не писать вручную `mutableStateOf`.
    

**Пример:**

```kotlin
@Composable
fun UserDataScreen(repository: UserRepository) {
    val userData by produceState<User?>(initialValue = null) {
        value = repository.loadUser() // suspend-функция
    }

    if (userData == null) {
        Text("Loading user...")
    } else {
        Text("Hello, ${userData!!.name}")
    }
}
```

---

### 4. **`rememberUpdatedState()`**

- Помогает правильно обновлять лямбды или данные внутри корутин, когда `Composable` перерисовывается.
    

---

### 5. **`DisposableEffect()`**

- Позволяет запускать корутину при появлении Composable и отменять её при удалении.
    

**Пример:**

```kotlin
@Composable
fun Timer() {
    var seconds by remember { mutableStateOf(0) }

    DisposableEffect(Unit) {
        val job = CoroutineScope(Dispatchers.Main).launch {
            while (true) {
                delay(1000)
                seconds++
            }
        }
        onDispose { job.cancel() }
    }

    Text("Time: $seconds")
}
```

---

## **Асинхронная загрузка данных в MVVM**

В архитектуре MVVM мы обычно используем **ViewModel**, где уже встроен `viewModelScope` для корутин.

**Пример ViewModel:**

```kotlin
class UserViewModel : ViewModel() {
    private val _user = MutableStateFlow<User?>(null)
    val user = _user.asStateFlow()

    fun loadUser() {
        viewModelScope.launch {
            val data = repository.loadUser()
            _user.value = data
        }
    }
}
```

**Composable:**

```kotlin
@Composable
fun UserScreen(viewModel: UserViewModel = viewModel()) {
    val user by viewModel.user.collectAsState()

    if (user == null) {
        Text("Loading...")
    } else {
        Text("Hello, ${user!!.name}")
    }
}
```

---

## **Когда использовать что?**

- **`LaunchedEffect`** — для разовых операций при появлении экрана.
    
- **`rememberCoroutineScope`** — для событий (например, нажатий кнопки).
    
- **`produceState`** — когда нужно асинхронно заполнить `State`.
    
- **ViewModel + StateFlow** — для работы с данными, которые переживают смену конфигурации.

- [[Coroutine Dispatchers]]
- [[Coroutine Builders]]
- [[Job]]
- [[Launched Effect]]
- [[Flow]]