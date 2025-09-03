### 🔹 Что такое ViewModel?

`ViewModel` — это архитектурный компонент Android, задача которого — **хранить и управлять состоянием UI**.  
Он “переживает” изменения конфигурации (например, поворот экрана) и обеспечивает разделение логики и интерфейса.

В связке с **Jetpack Compose** ViewModel играет роль "источника данных" для UI.  
UI в Compose является **функцией от состояния**, а `ViewModel` как раз и хранит это состояние (через `StateFlow`, `LiveData` или `MutableState`).

---

### 🔹 Почему ViewModel важен в Compose?

В Compose нет привычных `onSaveInstanceState()` или жёсткой привязки к `Activity/Fragment`.  
Compose-компоненты пересоздаются много раз (каждый `recomposition`), поэтому хранить состояние напрямую в UI — ненадёжно.

Поэтому:

- **ViewModel = источник правды** (Single Source of Truth, SSOT).
    
- UI → только отображает состояние.
    
- ViewModel → управляет логикой, запросами, обработкой событий.
    

---

### 🔹 Где и как создавать ViewModel?

В Compose есть функция `viewModel()`, которая автоматически:

- Создаёт `ViewModel` (если ещё не создан).
    
- Привязывает его к жизненному циклу `Activity` или `NavBackStackEntry` (если используешь Navigation).
    

Пример:

```kotlin
@Composable
fun RecipeScreen(
    viewModel: RecipeViewModel = viewModel()
) {
    val state by viewModel.uiState.collectAsState()

    Column {
        Text("Рецепты: ${state.recipes.size}")
        Button(onClick = { viewModel.loadRecipes() }) {
            Text("Обновить")
        }
    }
}
```

---

### 🔹 Состояние во ViewModel

Обычно в `ViewModel` используют **StateFlow**, **LiveData** или **mutableStateOf**.

Пример со `StateFlow` (рекомендуется для Compose):

```kotlin
class RecipeViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(RecipeUiState())
    val uiState: StateFlow<RecipeUiState> = _uiState

    fun loadRecipes() {
        viewModelScope.launch {
            val recipes = repository.getRecipes()
            _uiState.value = _uiState.value.copy(recipes = recipes)
        }
    }
}

data class RecipeUiState(
    val recipes: List<String> = emptyList(),
    val isLoading: Boolean = false,
    val error: String? = null
)
```

---

### 🔹 Подписка на состояние в Compose

В UI мы подписываемся на поток состояния через:

```kotlin
val state by viewModel.uiState.collectAsState()
```

или через:

```kotlin
val state = viewModel.uiState.collectAsStateWithLifecycle()
```

(рекомендуется использовать `collectAsStateWithLifecycle` из `androidx.lifecycle:lifecycle-runtime-compose`).

---

### 🔹 Где создавать ViewModel?

- В **Activity/Screen** (если это корневой экран).
    
- В **NavHost** (если используешь Navigation Compose).
    
- Можно передавать в `@Composable` через параметры (для тестов или DI).
    

---

### 🔹 Паттерн работы

1. UI → вызывает событие (например, нажал кнопку).
    
2. ViewModel → обрабатывает событие и меняет состояние.
    
3. UI → автоматически перерисовывается (recomposition).
    

---

✅ Итог:  
ViewModel в Jetpack Compose — это **мост между бизнес-логикой и UI**, который:

- Хранит состояние.
    
- Управляет данными и событиями.
    
- Переживает конфигурационные изменения.
    
- Делает UI декларативным и чистым.
