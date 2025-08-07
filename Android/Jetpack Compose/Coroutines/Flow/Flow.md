Потоки `Flow` в Android (Kotlin Coroutines) — это реактивный способ обработки асинхронных данных, таких как пользовательские действия, обновления базы данных или сетевые ответы. В связке с **Jetpack Compose** `Flow` становится мощным инструментом для **реактивного UI**, особенно в архитектуре **MVVM**.

## 📌 Что такое Flow?

`Flow` — это холодный асинхронный поток значений, т.е. значения начинают эмиттироваться только при **подписке**. Он похож на `LiveData`, но мощнее, гибче и работает в корутинах.


## 🔹Создание потока Flow
Чтобы создать поток `Flow` в Kotlin, можно использовать один из нескольких способов в зависимости от ситуации
### 1. Через `flow {}` — **основной способ**
```kotlin
import kotlinx.coroutines.flow.*

val numberFlow: Flow<Int> = flow {
    emit(1)
    emit(2)
    emit(3)
}
```
🔹 Здесь **вручную эммитируются значения** через `emit()` внутри корутины.  
📌 Под капотом используется `suspend` — можно вызывать `delay()`, `network`, `database` и т. д.

Пример с задержкой:
```kotlin
val flow = flow {
    for (i in 1..5) {
        emit(i)
        delay(1000)
    }
}
```

### 2. Через `flowOf(...)` — **из фиксированных значений**
```kotlin
val list = listOf(1, 2, 3)
val flow = list.asFlow()
```

## 🔹Генерация данных в поток Flow
### Базовая генерация
```kotlin
val simpleFlow = flow {
    emit(1)
    emit(2)
    emit(3)
}
```

### Генерация в цикле
```kotlin
val countingFlow = flow {
    for (i in 1..5) {
        emit(i)
        delay(1000) // имитируем асинхронную задержку (например, запрос к API)
    }
}
```
Такой поток можно использовать для **счетчиков**, **анимаций загрузки**, **таймеров** и т. д.

### Генерация из suspend-функции (например, сеть или база)
```kotlin
val userFlow = flow {
    val user = getUserFromNetwork() // suspend-функция
    emit(user)
}
```

### Обработка ошибок в процессе генерации
```kotlin
val safeFlow = flow {
    emit("Начало")
    val result = riskyOperation() // может выбросить исключение
    emit(result)
}.catch { e ->
    emit("Ошибка: ${e.message}")
}
```

## 🔹Поток как состояние и collectAsState
`Flow` как **состояние** в Jetpack Compose — это фундамент для **реактивного UI**. Именно с помощью `collectAsState()` можно сделать так, чтобы данные из потока автоматически обновляли интерфейс при изменении.

```kotlin
@Composable
fun MyScreen(viewModel: MyViewModel) {
    val value by viewModel.flow.collectAsState(initial = 0)
    Text("Текущее значение: $value")
}
```

🔹 `collectAsState()` подписывается на `Flow`  
🔹 Возвращает `State<T>`  
🔹 При каждом `emit()` из `Flow` происходит **рекомпозиция**  
🔹 Поток **отменяется автоматически**, когда Composable выходит из экрана

### 🔁 Как это работает

1. Ты создаешь `Flow` (например, в `ViewModel`)
    
2. В `Composable` вызываешь `.collectAsState(initial = ...)`
    
3. Когда `Flow` эмиттит новое значение — UI автоматически обновляется

### 🧪 Пример с `StateFlow` в ViewModel
```kotlin
class MyViewModel : ViewModel() {
    private val _counter = MutableStateFlow(0)
    val counter: StateFlow<Int> = _counter

    fun increment() {
        _counter.value += 1
    }
}
```

В UI:
```kotlin
@Composable
fun CounterScreen(viewModel: MyViewModel) {
    val count by viewModel.counter.collectAsState()

    Column {
        Text("Счетчик: $count")
        Button(onClick = { viewModel.increment() }) {
            Text("Увеличить")
        }
    }
}
```

#### `initial` — зачем нужен?

Если ты используешь `Flow<T>` (не `StateFlow`), ты **обязан** указать начальное значение:
```kotlin
val text by viewModel.textFlow.collectAsState(initial = "Загрузка...")
```
📌 Это нужно, потому что `Flow` может **не эмиттить сразу**, а UI должен отобразить что-то с самого начала.

### ⚠️ Важно помнить

- `collectAsState()` **выполняется в Composition**, и автоматически отменяется при уходе с экрана.
    
- Поток должен **работать бесконечно корректно** — без зависаний, ошибок и утечек.
    
- Если нужен контроль над подпиской — можно использовать `LaunchedEffect` и `collect()` вручную.

## 🔹 Преобразования потоков
Преобразование потока (`Flow`) — это процесс, при котором нужно **перехватить**, **отфильтровать** или **модифицировать** значения до того, как они попадут в UI или другую часть логики.  
Потоки в Kotlin Coroutines обладают богатым набором **операторов**, вдохновлённых `RxJava` и `Stream API`.
### 🧩 Основные операторы преобразования Flow

| Оператор               | Назначение                             |
| ---------------------- | -------------------------------------- |
| `map`                  | Преобразование значения                |
| `filter`               | Фильтрация по условию                  |
| `take` / `drop`        | Взять/пропустить первые N элементов    |
| `onEach`               | Побочные действия (например, лог)      |
| `debounce`             | Игнорирует частые значения             |
| `distinctUntilChanged` | Игнорирует повторения                  |
| `flatMapLatest`        | Переключается на новый вложенный поток |
| `catch`                | Обработка ошибок                       |
| `combine`              | Объединение нескольких потоков         |
| `zip`                  | Слияние потоков по парам               |

---

#### 🔹 1. `map {}` — преобразование значений

```kotlin
val numbers = flowOf(1, 2, 3).map { it * 10 }
// Выдаст: 10, 20, 30
```

---

#### 🔹 2. `filter {}` — фильтрация

```kotlin
val evens = flowOf(1, 2, 3, 4).filter { it % 2 == 0 }
// Выдаст: 2, 4
```

---

#### 🔹 3. `onEach {}` — побочные эффекты (например, логирование)

```kotlin
val flow = flowOf("A", "B", "C")
    .onEach { println("Value: $it") }
```

---

#### 🔹 4. `take(n)` и `drop(n)`

```kotlin
flowOf(1, 2, 3, 4).take(2) // → 1, 2
flowOf(1, 2, 3, 4).drop(2) // → 3, 4
```

---

#### 🔹 5. `debounce()` — для ввода пользователя

```kotlin
searchQuery
    .debounce(300)
    .flatMapLatest { query -> search(query) }
```

📌 Используется для **обработки текста** из `TextField`, чтобы не запускать запрос при каждом символе.

---

#### 🔹 6. `distinctUntilChanged()`

```kotlin
flowOf(1, 1, 2, 2, 3).distinctUntilChanged()
// → 1, 2, 3
```

📌 Убирает дублирующиеся подряд значения

---

#### 🔹 7. `flatMapLatest {}` — для вложенных потоков

```kotlin
val queryFlow: Flow<String> = ...
val resultFlow = queryFlow.flatMapLatest { query ->
    repository.search(query) // Flow<List<Result>>
}
```

📌 Отлично подходит для **поиска**: отменяет предыдущий запрос, если пользователь печатает новое.

---

#### 🔹 8. `catch {}` — обработка ошибок

```kotlin
val safeFlow = flow {
    emit(loadData()) // может выбросить исключение
}.catch { e ->
    emit(emptyList()) // или emit("Ошибка: ${e.message}")
}
```

---

#### 🔹 9. `combine()` — объединение потоков (по последнему значению каждого)

```kotlin
val nameFlow = flowOf("Alex")
val ageFlow = flowOf(25)

val combined = nameFlow.combine(ageFlow) { name, age ->
    "$name — $age лет"
}
```

---

#### 🔹 10. `zip()` — объединение потоков **попарно**

```kotlin
val a = flowOf(1, 2, 3)
val b = flowOf("A", "B", "C")

val zipped = a.zip(b) { number, letter -> "$number$letter" }
// → "1A", "2B", "3C"
```

---

### 📦 Пример полного преобразования

```kotlin
val flow = (1..10).asFlow()
    .filter { it % 2 == 0 }          // Только чётные
    .map { it * it }                 // Возведение в квадрат
    .onEach { delay(100) }           // Имитируем задержку
    .catch { emit(-1) }              // В случае ошибки
```

---

### 🧠 Подсказки

|Что нужно сделать|Оператор|
|---|---|
|Преобразовать значения|`map`|
|Оставить только нужные|`filter`|
|Избежать повторных вызовов при вводе|`debounce`|
|Объединить два потока|`combine` / `zip`|
|Обработать ошибку|`catch`|
|Отменять старые вложенные потоки|`flatMapLatest`|
## StateFlow и SharedState

- [[StateFlow]]
- [[SharedFlow]]