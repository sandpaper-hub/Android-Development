В Kotlin корутины создаются с помощью **строителей корутин** — специальных функций, которые управляют запуском, контекстом и способом выполнения корутин. Строители — это то, с чего **начинается выполнение корутины**.

---

## 🚀 Основные строители корутин

|Строитель|Возвращает|Блокирует поток?|Описание|
|---|---|---|---|
|`launch`|`Job`|❌|Запускает фоновую задачу без возвращаемого результата|
|`async`|`Deferred<T>`|❌|Запускает задачу с результатом (`await()` для получения)|
|`runBlocking`|`T`|✅|Блокирует текущий поток, пока корутина не завершится (используется в `main` или тестах)|
|`withContext`|`T`|❌|Переключает контекст и возвращает результат|
|`coroutineScope`|`T`|❌|Создаёт область корутин и **ждёт завершения всех вложенных**|

---

## 📌 `launch`

- Используется, когда **результат не нужен**.
    
- Возвращает `Job` — можно отменить или проверить статус.
    
- Часто используется в `ViewModel` и `rememberCoroutineScope`.
    

```kotlin
launch(Dispatchers.IO) {
    val data = repository.loadData()
    withContext(Dispatchers.Main) {
        uiState.value = data
    }
}
```

---

## 📌 `async`

- Используется, когда нужен **результат**.
    
- Возвращает `Deferred<T>`, результат можно получить через `await()`.
    

```kotlin
val deferred1 = async { loadPart1() }
val deferred2 = async { loadPart2() }

val result = deferred1.await() + deferred2.await()
```

> ⚠️ Если не вызвать `await()`, задача всё равно будет выполняться, но результат будет потерян.

---

## 📌 `runBlocking`

- Блокирует **поток**, пока корутина не завершится.
    
- Используется **только в тестах** или `main()` функциях (не в UI!).
    

```kotlin
fun main() = runBlocking {
    println("Start")
    delay(1000)
    println("End")
}
```

---

## 📌 `withContext`

- Переключает контекст (например, на `Dispatchers.IO`) и **возвращает результат**.
    
- Приостановливает текущую корутину.
    

```kotlin
val data = withContext(Dispatchers.IO) {
    repository.getData()
}
```

---

## 📌 `coroutineScope`

- Создаёт вложенную область корутин. Пока **все дочерние корутины не завершатся**, `coroutineScope` не завершится.
    
- Удобно для параллельных задач с общей отменой.
    

```kotlin
suspend fun doTasks() = coroutineScope {
    launch { task1() }
    launch { task2() }
    // дождётся завершения обеих
}
```

---

## 🧠 Когда какой использовать?

|Цель|Строитель|
|---|---|
|Запустить фоновую задачу без результата|`launch`|
|Выполнить параллельно с результатом|`async + await`|
|Переключить поток и получить результат|`withContext`|
|Синхронный вызов корутины (в `main`)|`runBlocking`|
|Создать область с дочерними задачами|`coroutineScope`|

---

## 💡 Compose + launch

```kotlin
@Composable
fun MyScreen() {
    val scope = rememberCoroutineScope()

    Button(onClick = {
        scope.launch {
            val result = withContext(Dispatchers.IO) {
                repository.load()
            }
            // обновить UI
        }
    }) {
        Text("Load")
    }
}
```
