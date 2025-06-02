**Производное состояние (derived state)** в Jetpack Compose — это вспомогательный механизм, который позволяет **эффективно вычислять и кэшировать** значения, зависящие от одного или нескольких «обычных» состояний, так чтобы **не вызывать лишних переработок (recomposition)**. Грубо говоря, когда у вас есть несколько базовых `State`, и вам нужно от них вывести новое значение, лучше использовать именно производное состояние, а не вычислять его непосредственно внутри `@Composable`.

## Почему это важно

Представьте, что у вас есть два состояния:

```kotlin
var firstName by remember { mutableStateOf("") }
var lastName by remember { mutableStateOf("") }
```

И вы хотите отобразить полное имя, объединяя их:

```kotlin
@Composable
fun UserProfile() {
    var firstName by remember { mutableStateOf("") }
    var lastName  by remember { mutableStateOf("") }

    Column {
        TextField(
            value = firstName,
            onValueChange = { firstName = it },
            label = { Text("Имя") }
        )
        TextField(
            value = lastName,
            onValueChange = { lastName = it },
            label = { Text("Фамилия") }
        )

        // Пример «разового» вычисления полного имени
        Text("Полное имя: $firstName $lastName")
    }
}
```

Каждый раз, когда меняется `firstName` **или** `lastName`, Compose будет **повторно композировать весь** `UserProfile`, включая строчку `Text("Полное имя: $firstName $lastName")`. В простом случае это не проблема. Но если логика более сложная — например, форматирование, подсчёт каких-то данных, или если этот фрагмент используется во множественных местах — нерационально каждый раз пересчитывать выражение `"Полное имя: $firstName $lastName"` (хотя это очень дёшево). В более серьёзных примерах вычисление может быть затратным.

Если же вы передаёте это «полное имя» в десятки других `@Composable`, и хотите, чтобы **обновление полного имени происходило лишь тогда, когда меняется одно из «базовых» состояний**, но при этом **Compose не сбрасывал лишние участки** UI, то на помощь приходит **производное состояние**.

## Как устроено производное состояние

### `derivedStateOf`

Самый простой и часто используемый API для этого — функция `derivedStateOf`. Она принимает в лямбде выражение, основанное на других `State`, и возвращает объект типа `State<T>`, который **пересчитывается только тогда, когда хотя бы одно из зависимых состояний изменилось**.

```kotlin
val fullName by derivedStateOf { "$firstName $lastName" }
```

При такой конструкции:

1. Compose **использует кэш**: лямбда `{ "$firstName $lastName" }` выполнится **единожды** после того, как вы впервые подпишетесь на `fullName`.
    
2. После этого Compose будет **наблюдать** за `firstName` и `lastName` внутри этой лямбды.
    
3. При изменении любого из них — пересчитается `fullName`.
    
4. До тех пор, пока `firstName` и `lastName` остаются прежними, `fullName` **не меняется**, а значит, UI, который подписан на `fullName`, **не будет лишний раз перерисовываться**.

#### Пример: аккуратное вычисление полного имени
```kotlin
@Composable
fun UserProfileOptimized() {
    var firstName by remember { mutableStateOf("") }
    var lastName  by remember { mutableStateOf("") }

    // 1) Производное состояние: fullName будет пересчитываться только когда firstName или lastName меняются
    val fullName by remember {
        derivedStateOf {
            // Допустим, тут более сложная логика, но для примера просто строка
            "${firstName.trim().capitalize()} ${lastName.trim().capitalize()}"
        }
    }

    Column(Modifier.padding(16.dp)) {
        TextField(
            value = firstName,
            onValueChange = { firstName = it },
            label = { Text("Имя") }
        )
        Spacer(modifier = Modifier.height(8.dp))
        TextField(
            value = lastName,
            onValueChange = { lastName = it },
            label = { Text("Фамилия") }
        )

        Spacer(modifier = Modifier.height(16.dp))
        // 2) Используем fullName
        Text(
            text = "Полное имя: $fullName",
            style = MaterialTheme.typography.titleMedium
        )
    }
}
```

**Почему `remember { derivedStateOf { … } }`?**

- `derivedStateOf` сам по себе создаёт `State<T>`, но без `remember` каждый вызов `UserProfileOptimized` при recomposition создаст новый экземпляр.
    
- Поэтому мы оборачиваем в `remember { ... }`, чтобы сохранить один объект производного состояния во время жизненного цикла компонента.

## Когда использовать `derivedStateOf`

1. **Сложные вычисления**  
    Если вычисление выходного значения ресурсоёмко (сложное форматирование, агрегация, фильтрация, сортировка списка и т. п.), и вы хотите, чтобы это происходило **лишь по факту изменения зависимостей**.
    
2. **Формирование кэшей**  
    Часто нужно сгенерировать какую-то промежуточную коллекцию (напр. отфильтрованный или отсортированный список) на основе неизменяемого исходного списка и каких-то параметров. Если без `derivedStateOf` вы будете фильтровать список внутри `@Composable` при каждом recomposition, то это может быть плохо с точки зрения производительности.

```kotlin
var query by remember { mutableStateOf("") }
val items = remember { /* огромный список данных */ }

// Производное состояние: filteredItems пересчитывается только когда items или query меняются
val filteredItems by remember {
    derivedStateOf {
        items.filter { it.contains(query, ignoreCase = true) }
    }
}

LazyColumn {
    items(filteredItems) { item ->
        Text(item)
    }
}
```

3. **Избежать ««лишних» recomposition**  
	Допустим, внутри одного composable лежит несколько `Text`, подписанных на одно исходное `State`. Один из них завязан на производном значении, другой — на «сыром». При изменении сырого состояния все потребители пересобираются, но если вы хотите «группировать» вычисления, чтобы не гонять одно и то же выражение в разных местах, используйте `derivedStateOf`.

## Важные детали и подводные камни

1. **Кэширование**  
    `derivedStateOf` **запоминает** последнее вычисленное значение и **не пересчитывает** лямбду, если её зависимости (State, которые использовались внутри) не менялись.
    
2. **Нужно помнить о `remember`**  
    Если написать просто

```kotlin
val fullName by derivedStateOf { "$firstName $lastName" }
```

без `remember`, то при каждом recomposition вы будете **создавать новый экземпляр** производного состояния, что нивелирует смысл кэширования — лучше всегда оборачивать в `remember { ... }`.

3. **Не стоит «открывать» mutableState внутри лямбды**  
	Если вы обращаетесь к `MutableState` (или `StateFlow`, `LiveData`, `derivedStateOf`) внутри `derivedStateOf`, Compose автоматически «подпишется» на эти изменения. Однако избегайте побочных эффектов внутри лямбды.
```kotlin
// НЕПРАВИЛЬНО: побочный эффект при чтении
val dangerous by remember {
    derivedStateOf {
        // Такой код нарушает принципы: нельзя тут вызывать getters, которые в свою очередь что-то меняют…
        someMutableState.value++  
    }
}
```

4. **Производное состояние против `snapshotFlow`**

	- `derivedStateOf` — не `Flow`, а `State`. Предназначен для «только чтения» и кэширования вычислений.
    
	- Если нужно **реагировать** на изменения состояния и запускать какой-то побочный эффект (запрос в сеть, запись в БД и т. п.), а не просто вычислять новое значение, лучше использовать `snapshotFlow { ... }` в `LaunchedEffect`.
```kotlin
// Пример: реагировать на изменение query и отправлять сетевой запрос
LaunchedEffect(Unit) {
    snapshotFlow { query }
        .debounce(300)
        .distinctUntilChanged()
        .collectLatest { q ->
            // Выполнить поиск по q
        }
}
```

Здесь `snapshotFlow` превращает `State<T>` в `Flow<T>`, чтобы вы могли «мигрировать» в корутину при изменении.

5. **Производное состояние и сложные коллекции**  
	Если вы делаете `derivedStateOf { items.filter { … } }`, помните, что при фильтрации создаётся **новый список**. Поэтому при каждом изменении зависимости (например, изменился один элемент в исходном списке), вы получите новый объект списка, и все `LazyColumn(items = filteredItems)` перерисуются. Чтобы избежать лишних перерисовок при большом объёме данных и нечастых изменения, можно:
    
    - Использовать **immutable** структуры (например, `SnapshotStateList`), но и в этом случае `filter` создаёт новый лист.
        
    - В некоторых случаях применять библиотеки, оптимизированные для диффинга (например, `ListAdapter` в View-среде или `LazyPagingItems` из Paging Compose). Но это уже отдельные истории.
        
6. **Глубокие цепочки**  
    Можно вкладывать `derivedStateOf` друг в друга, если, например, результат одной фильтрации нужен для второй. Однако старайтесь не создавать слишком «глубоких» цепочек, чтобы не потерять контроль над производительностью.

## Примеры задач и кода

### Пример 1: Агрегированное состояние

Допустим, у вас есть список чисел, и вы хотите одновременно вычислять **сумму** и **максимальное** значение. Вместо того, чтобы в каждом `Text` писать `numbers.sum()` и `numbers.maxOrNull()`, сделайте производное состояние:
```kotlin
@Composable
fun NumbersStatsScreen() {
    val numbers = remember { mutableStateListOf<Int>() }

    // добавление числа (для примера)
    var next by remember { mutableStateOf(1) }
    Button(onClick = { 
        numbers.add(next)
        next++
    }) {
        Text("Добавить число $next")
    }
    Spacer(Modifier.height(16.dp))

    val stats by remember {
        derivedStateOf {
            // Здесь любая сложная логика
            val sum = numbers.sum()
            val max = numbers.maxOrNull() ?: 0
            val min = numbers.minOrNull() ?: 0
            Triple(sum, max, min)
        }
    }

    Text("Сумма: ${stats.first}")
    Text("Максимум: ${stats.second}")
    Text("Минимум: ${stats.third}")
}
```

- `derivedStateOf` пересчитывает `stats` лишь когда `numbers` изменился (при добавлении/удалении элемента).
    
- Если, например, вы меняете UI (реконструкция) по другой причине (изменился другой `State` в окружающем контексте), `stats` не пересчитывается заново, пока `numbers` не изменится.

### Пример 2: Валидация полей формы
```kotlin
@Composable
fun RegistrationForm() {
    var email by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }

    // Производное состояние: валидность email
    val isEmailValid by remember {
        derivedStateOf {
            Patterns.EMAIL_ADDRESS.matcher(email).matches()
        }
    }

    // Производное состояние: валидность пароля (минимум 8 символов)
    val isPasswordValid by remember {
        derivedStateOf {
            password.length >= 8
        }
    }

    // Производное состояние: оба поля валидны?
    val isFormValid by remember {
        derivedStateOf {
            isEmailValid && isPasswordValid
        }
    }

    Column(Modifier.padding(16.dp)) {
        TextField(
            value = email,
            onValueChange = { email = it },
            label = { Text("Email") },
            isError = !isEmailValid && email.isNotBlank()
        )
        if (!isEmailValid && email.isNotBlank()) {
            Text(
                text = "Неверный формат email",
                color = Color.Red,
                style = MaterialTheme.typography.bodySmall
            )
        }

        Spacer(Modifier.height(8.dp))

        TextField(
            value = password,
            onValueChange = { password = it },
            label = { Text("Пароль") },
            isError = !isPasswordValid && password.isNotBlank(),
            visualTransformation = PasswordVisualTransformation()
        )
        if (!isPasswordValid && password.isNotBlank()) {
            Text(
                text = "Пароль должен быть не менее 8 символов",
                color = Color.Red,
                style = MaterialTheme.typography.bodySmall
            )
        }

        Spacer(Modifier.height(16.dp))

        Button(
            onClick = { /* регистрация */ },
            enabled = isFormValid
        ) {
            Text("Зарегистрироваться")
        }
    }
}
```

- Каждый раз, когда изменяется `email` или `password`, пересчитаются соответствующие флаги `isEmailValid` и `isPasswordValid`.
    
- А `isFormValid` пересчитывается только когда один из предыдущих флагов поменялся.
    
- Compose **не будет** тратить лишние ресурсы, чтобы «заново» вычислять валидацию, если, скажем, изменился другой кусок Compose, не связанный с этими полями.