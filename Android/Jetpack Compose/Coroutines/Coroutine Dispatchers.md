В Kotlin Coroutines **диспетчер (dispatcher)** определяет, **в каком потоке** или **на каком пуле потоков** будет выполняться корутина. Это важный элемент управления асинхронным кодом: ты выбираешь, выполнять ли задачу в главном потоке, в фоне, или в специальной среде (например, с ограничением количества одновременных задач).

---

## 🚦 Основные диспетчеры

### 1. **`Dispatchers.Main`**

- Используется для **UI-операций**.
    
- Выполняет код в **главном (UI) потоке**.
    
- В Android всегда присутствует.
    
- ⚠️ Нельзя здесь делать тяжёлую работу — это приведёт к зависанию UI.
    

**Пример:**

```kotlin
withContext(Dispatchers.Main) {
    text = "UI обновлено"
}
```

---

### 2. **`Dispatchers.IO`**

- Для **ввода/вывода** (сеть, файлы, БД).
    
- Использует **оптимизированный пул потоков** (обычно до 64 потоков).
    
- Подходит для `Room`, Retrofit и других I/O задач.
    

**Пример:**

```kotlin
withContext(Dispatchers.IO) {
    val data = repository.getUser()
}
```

---

### 3. **`Dispatchers.Default`**

- Для **тяжёлых вычислений**: сортировки, парсинга, работы с большими коллекциями.
    
- Использует общий пул потоков = числу ядер.
    

**Пример:**

```kotlin
withContext(Dispatchers.Default) {
    val result = heavyComputation()
}
```

---

### 4. **`Dispatchers.Unconfined`**

- Запускает корутину **в текущем потоке**, но **возобновление** может происходить в **другом потоке**.
    
- Обычно **не рекомендуется**, за исключением специфичных случаев.
    

---

### 5. **`newSingleThreadContext("MyThread")`**

- Создаёт **отдельный поток**.
    
- Используется редко (например, для тестов или задач с потокобезопасностью).
    

---

## 🔄 `withContext()` vs `launch(Dispatchers.X)`

- `withContext` — переключает контекст **и ждёт** выполнения результата.
    
- `launch(Dispatchers.X)` — **запускает** фоновую задачу, не блокируя текущий поток.
    

**Пример `withContext`:**

```kotlin
suspend fun loadUser(): User {
    return withContext(Dispatchers.IO) {
        repository.getUser()
    }
}
```

**Пример `launch`:**

```kotlin
viewModelScope.launch(Dispatchers.IO) {
    val user = repository.getUser()
}
```

---

## 📍Как это применимо в Jetpack Compose?

Внутри Composable ты обычно не указываешь `Dispatchers` напрямую. Вместо этого:

- `LaunchedEffect` и `rememberCoroutineScope().launch` по умолчанию работают в `Dispatchers.Main`.
    
- Для фоновых задач ты используешь `withContext(Dispatchers.IO)` внутри этих корутин.
    

---

## 📌 Под капотом ViewModel

ViewModel использует **`viewModelScope`**, который:

- по умолчанию запускает корутины в `Dispatchers.Main`,
    
- но ты можешь переключаться на другие диспетчеры.
    

**Пример:**

```kotlin
viewModelScope.launch {
    val result = withContext(Dispatchers.IO) {
        repository.getData()
    }
    _state.value = result
}
```

---

## 🧠 Когда какой Dispatcher использовать?

|Dispatcher|Когда использовать|
|---|---|
|`Main`|Обновление UI|
|`IO`|Сеть, БД, файлы|
|`Default`|Тяжёлые вычисления|
|`Unconfined`|Специфические случаи, тесты|
|`newSingleThread`|Когда нужен отдельный поток (редко)|
