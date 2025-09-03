## 🔹 Что такое MVVM?

MVVM — это архитектурный шаблон, который помогает разделить:

- **Model** — данные и бизнес-логика (репозитории, API, база данных).
    
- **View** — пользовательский интерфейс (в Compose это `@Composable` функции).
    
- **ViewModel** — посредник, который хранит состояние, управляет логикой UI и связывает View ↔ Model.
    

Идея:

> UI не думает сам, он только рисует.  
> ViewModel хранит «истину» о состоянии экрана.  
> Model даёт данные из сети/БД.

---

## 🔹 Роли компонентов

### 1. Model

- Репозитории, DAO, API-клиенты.
    
- Отвечают за получение и хранение данных.
    
- Не знают ничего о UI.
    

Пример:

```kotlin
class RecipeRepository {
    suspend fun getRecipes(): List<String> {
        // допустим, запрос в сеть или БД
        return listOf("Пицца", "Паста", "Суши")
    }
}
```

---

### 2. View (UI)

- Это чистые Composable функции.
    
- Подписываются на состояние ViewModel.
    
- Ничего сами не загружают и не хранят.
    

Пример:

```kotlin
@Composable
fun RecipeScreen(viewModel: RecipeViewModel = viewModel()) {
    val state by viewModel.uiState.collectAsState()

    Column {
        if (state.isLoading) {
            CircularProgressIndicator()
        } else {
            state.recipes.forEach {
                Text(text = it)
            }
        }

        Button(onClick = { viewModel.loadRecipes() }) {
            Text("Обновить")
        }
    }
}
```

---

### 3. [[ViewModel]]

- Переживает повороты экрана.
    
- Хранит состояние экрана (`StateFlow`, `LiveData`, `mutableStateOf`).
    
- Работает с репозиторием и обновляет `uiState`.
    

Пример:

```kotlin
class RecipeViewModel(
    private val repository: RecipeRepository = RecipeRepository()
) : ViewModel() {

    private val _uiState = MutableStateFlow(RecipeUiState())
    val uiState: StateFlow<RecipeUiState> = _uiState

    fun loadRecipes() {
        viewModelScope.launch {
            _uiState.value = _uiState.value.copy(isLoading = true)
            val recipes = repository.getRecipes()
            _uiState.value = RecipeUiState(recipes = recipes, isLoading = false)
        }
    }
}

data class RecipeUiState(
    val recipes: List<String> = emptyList(),
    val isLoading: Boolean = false
)
```

---

## 🔹 Как всё работает вместе?

1. Пользователь нажал кнопку → событие уходит во ViewModel.
    
2. ViewModel запускает загрузку → обновляет `uiState`.
    
3. UI автоматически реагирует и перерисовывается (recomposition).
    

---

## 🔹 Плюсы MVVM

✅ Разделение ответственности (UI не зависит от логики).  
✅ Хорошо работает с Jetpack Compose (UDF — однонаправленный поток данных).  
✅ ViewModel переживает конфигурационные изменения.  
✅ Легко тестировать бизнес-логику (ViewModel без Android зависимостей).

---

## 🔹 Минусы MVVM

⚠️ Иногда ViewModel может разрастаться («god object»).  
⚠️ Требует дисциплины: UI не должен напрямую менять данные, только через ViewModel.

---

## 🔹 Где используется

- Google официально рекомендует MVVM для Android (особенно с Compose).
    
- Большинство проектов сегодня пишут именно так.
    
- В больших приложениях MVVM часто комбинируют с **Clean Architecture** (чтобы отделить UseCase-слой).

