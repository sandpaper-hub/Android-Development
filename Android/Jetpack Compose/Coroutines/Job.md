В корутинах Kotlin объект **`Job`** — это базовая единица работы, представляющая собой **контролируемую задачу**: она может быть **отменена**, **приостановлена**, **проверена на завершение** и даже **дождана**.

---

## 🧩 Что такое `Job`?

`Job` — это интерфейс из kotlinx.coroutines, который реализуют все **запущенные корутины**, независимо от того, используется ли `launch`, `async` или другой строитель.

Он позволяет:

- отменить корутину;
    
- проверить её статус;
    
- дождаться её завершения;
    
- управлять иерархией корутин (родитель — потомок).
    

---

## 🚀 Как получить `Job`

### Примеры:

```kotlin
val job = scope.launch {
    delay(3000)
    println("Task done!")
}

job.cancel() // отменяет выполнение
```

---

## 🔍 Основные методы и свойства `Job`

|Метод / свойство|Описание|
|---|---|
|`cancel()`|Отменяет выполнение корутины|
|`isActive`|Корутину ещё можно выполнять|
|`isCompleted`|Корутинa завершена (успешно или с ошибкой)|
|`isCancelled`|Корутинa отменена|
|`join()`|Приостанавливает выполнение текущей корутины, пока `job` не завершится|
|`invokeOnCompletion()`|Коллбек, вызываемый при завершении|

---

## 🧪 Пример: отмена задачи

```kotlin
val job = scope.launch {
    repeat(10) { i ->
        delay(500)
        println("Step $i")
    }
}

LaunchedEffect(Unit) {
    delay(2000)
    job.cancel() // остановит корутину после ~4 шагов
}
```

---

## ⛓ Иерархия `Job`: родитель и дочерние корутины

- Если ты запускаешь корутину внутри `coroutineScope` или `viewModelScope`, она **наследует родительский Job**.
    
- Если родитель отменён — **все дочерние корутины автоматически отменяются**.
    

```kotlin
val parentJob = CoroutineScope(Dispatchers.Default).launch {
    launch { doWork1() }
    launch { doWork2() }
}

parentJob.cancel() // отменит все вложенные
```

---

## 📌 `Job` в ViewModel

ViewModel предоставляет `viewModelScope`, в котором все `launch` уже создаются с `Job`. Когда ViewModel уничтожается, **все её корутины автоматически отменяются**.

```kotlin
class MyViewModel : ViewModel() {
    fun load() {
        val job: Job = viewModelScope.launch {
            delay(1000)
        }
        // job можно сохранить и отменить вручную, если нужно
    }
}
```

---

## 💡 Особенности:

- Если ты вызываешь `cancel()` — **корутина завершится, но не сразу**, а только когда **встретит отменяемую точку** (`delay`, `yield`, `withContext`, `suspend`).
    
- Если внутри тяжёлая синхронная работа — отмена не сработает.
    

---

## 🎯 `Job` vs `Deferred`

|Тип|Описание|
|---|---|
|`Job`|Задача без результата|
|`Deferred<T>`|Задача с результатом (`async`)|
## Полный пример:
```kotlin
@Composable  
@Suppress("FunctionName")  
fun JobExample(modifier: Modifier) {  
    val scope = rememberCoroutineScope()  
    var job by remember { mutableStateOf<Job?>(null) }  
    var textValue by remember { mutableStateOf("Waiting...") }  
  
    ConstraintLayout(modifier = modifier.then(Modifier.fillMaxSize())) {  
        val (text, buttonStart, buttonCancel) = createRefs()  
  
        createVerticalChain(text, buttonStart, buttonCancel, chainStyle = ChainStyle.Packed)  
  
        Text(textValue, modifier = Modifier.constrainAs(text) {  
            centerHorizontallyTo(parent)  
        })  
  
        Button(onClick = {  
            job = scope.launch {  
                textValue = "Process's started"  
                delay(5000)  
                textValue = "Ready!"  
            }  
        }, modifier = Modifier.constrainAs(buttonStart) {  
            centerHorizontallyTo(parent)  
        }) {  
            Text("Start")  
        }  
  
        Button(onClick = {  
            job = scope.launch {  
                job?.cancel()  
                textValue = "Canceled"  
            }  
        }, modifier = Modifier.constrainAs(buttonCancel) {  
            centerHorizontallyTo(parent)  
        }) {  
            Text("Cancel")  
        }  
    }
}
```