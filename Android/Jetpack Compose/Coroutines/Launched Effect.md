`LaunchedEffect` и `SideEffect` — это **побочные эффекты** (`Side-Effects API`), которые позволяют выполнять действия **вне обычного декларативного рендера UI**, например:

- запуск корутин;
    
- вызов suspend-функций;
    
- логирование, навигация, анимации;
    
- вызов функций, изменяющих состояние вне Compose.
    

Они **важны**, потому что **Compose не разрешает запускать побочные действия напрямую в теле Composable-функций** — это нарушает чистоту функций.

---

## 🧭 `LaunchedEffect`

### 📌 Что делает:

- запускает **корутину** при **первом появлении Composable** или при **изменении ключей (keys)**.
    
- работает внутри `@Composable`, но **не перезапускается при каждой перерисовке**, только при изменении ключей.
    

### 📦 Сигнатура:

```kotlin
@Composable
fun LaunchedEffect(key1: Any?, block: suspend CoroutineScope.() -> Unit)
```

---

### ✅ Пример: запуск корутины один раз

```kotlin
@Composable
fun Greeting() {
    var message by remember { mutableStateOf("Привет...") }

    LaunchedEffect(Unit) {
        delay(2000)
        message = "Добро пожаловать!"
    }

    Text(message)
}
```

🔹 Здесь `LaunchedEffect(Unit)` гарантирует, что код выполнится **один раз при входе в Composable**.

---

### 🔁 Пример: реагирование на изменение ключа

```kotlin
@Composable
fun UserScreen(userId: Int) {
    var userData by remember { mutableStateOf<User?>(null) }

    LaunchedEffect(userId) {
        userData = repository.loadUser(userId) // suspend-функция
    }

    Text(userData?.name ?: "Загрузка...")
}
```

🔹 При изменении `userId`, корутина **перезапускается**, старое выполнение отменяется.

---

## ⚙️ `SideEffect`

### 📌 Что делает:

- **не запускает корутину**, а просто выполняет **однократный вызов** после успешного **композинга (перерисовки)**.
    
- работает **на главном потоке**;
    
- **не перезапускается при recomposition**, если ключи не изменились.
    

### 📦 Сигнатура:

```kotlin
@Composable
fun SideEffect(block: () -> Unit)
```

---

### ✅ Пример: лог после рендера

```kotlin
@Composable
fun LogComposableState(value: String) {
    SideEffect {
        Log.d("MyTag", "Composable отрисовался с $value")
    }

    Text("Текущее значение: $value")
}
```

🔹 Это используется, например, чтобы обновить что-то во внешнем API, которое нельзя использовать в `remember` (например, сторонний объект, не контролируемый Compose).

---

## 🤔 Когда что использовать?

|Сценарий|Что использовать|
|---|---|
|Запустить корутину при появлении|`LaunchedEffect(Unit)`|
|Перезапустить при изменении значения|`LaunchedEffect(key)`|
|Логирование, вызов внешних API|`SideEffect`|
|Использование `suspend`-функции|`LaunchedEffect`|
|Анимации, события, инициализация|`LaunchedEffect`|

---

## 🧠 Что будет, если не использовать их?

```kotlin
@Composable
fun BadComposable() {
    // ❌ Плохо — delay в UI-теле (не работает)
    delay(1000)
    Text("Загрузка...")
}
```

🔴 Это вызовет **ошибку компиляции или runtime** — `Composable`-функции должны быть **чистыми** (без побочных действий внутри тела).
