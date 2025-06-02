Однонаправленный поток данных (unidirectional data flow) представляет подход, при котором состояние компонента не может напрямую изменяться дочерними, вложенными компонентами. Применение однонаправленного потока данных упрощает создание модульных, независимых друг от друга компонентов и, как следствие, упрощает разработку приложения и его тестирование.

## 1. Основные принципы UDF

1. **Есть единый источник состояния**  
    Вся информация, необходимая для построения экрана (UI), хранится в одном объекте (или наборе объектов) — обычно в ViewModel (или в хранилище / менеджере состояния). Этот объект называют «Model» или «State».
    
2. **UI читает состояние, но не меняет его напрямую**  
    Компоненты (`@Composable`) «рисуют» интерфейс на основании того, что сейчас находится в State. Если внутри Compose-функций возникает `recompose`, они заново считывают текущее значение State и перерисовываются.
    
3. **События идут «вверх»**  
    Когда пользователь нажал кнопку, ввёл текст, прокрутил список и т. д. — UI НЕ меняет State самостоятельно. Вместо этого он **генерирует событие** (intent, action) — колбэк, lambda, вызов метода ViewModel. Таким образом, все мутации State происходят в едином месте (ViewModel, reducer, use-case и т.д.).
    
4. **State обновляется централизованно**  
    После того как ViewModel (или другой «менеджер») получил событие от UI, он меняет State (например, через `MutableStateFlow`, `LiveData` или `mutableStateOf`). Compose «слушает» эти изменения и автоматически пересобирает соответствующие UI-элементы.
    
5. **Цикл замыкается**
    
    - «ViewModel/state» → (поток) → «UI (Composable)».
        
    - «Пользовательское действие (клик/ввод/... )» → (Intent/Event) → «ViewModel/state» → (новое состояние) → «UI».

Схематично:

```markdown
┌───────────────┐         ┌───────────────┐
│     Model/    │ ───State──▶     UI       │
│   ViewModel   │           │ (Composable) │
└────▲──────────┘           └────▲──────────┘
     │                               │
     │   Event / Intent (клик, ввод) │
     └───────────────────────────────┘
```

## 2. Зачем нужен такой подход

1. **Предсказуемость**  
    Поскольку все изменения State происходят в одном месте, легко понять, откуда и зачем возникают мутации, и гарантировать, что никаких «побочных» эффектов не происходит прямо в слое представления.
    
2. **Чёткое разделение ответственности**
    
    - UI отвечает только за отрисовку, получение кликов и вводов;
        
    - ViewModel (Model) отвечает за бизнес-логику, загрузку/сохранение данных и управление состоянием.  
        Компоненты не «трогают» данные напрямую — они лишь читают и посылают сигналы, что произошло событие.
        
3. **Лёгкость тестирования**  
    Можно проверить ViewModel «в отрыве» от UI: подать в него события, посмотреть, как меняется State. А Compose-функции можно протестировать на основе разного State, не имитируя логику.
    
4. **Упрощённая отладка**  
    Так как State поток «однонаправленный», можно вставить точки логирования или использовать Time Travel Debugging (в Jetpack Compose есть инструменты, которые позволяют «перематывать» состояние назад) и точно знать, где и когда что поменялось.
    
5. **Композиция и переиспользование**  
    Stateless-компоненты (т. е. те, которые **не хранят состояние внутри себя**, а получают его только через параметры) можно легко реплицировать и переиспользовать в разных экранах, просто передавая в них нужный State и колбэки.

### Пример без архитектуры

```kotlin
@Composable  
@Suppress("FunctionName")  
fun SimpleUdfExample() {  
    var count by rememberSaveable { mutableIntStateOf(0) }  
    var logMessages by rememberSaveable { mutableStateOf(listOf<String>()) }  
  
    Column(  
        modifier = Modifier  
            .fillMaxWidth()  
            .height(300.dp)  
            .padding(12.dp)  
            .border(2.dp, MaterialTheme.colorScheme.primaryContainer, RoundedCornerShape(8.dp))  
            .padding(12.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        Text("Current count: $count", style = MaterialTheme.typography.titleLarge)  
        Spacer(modifier = Modifier.height(12.dp))  
        Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {  
            Button(onClick = {  
                count++  
                logMessages = logMessages + "Clicked \"+\": count is $count "  
            }) {  
                Text("+")  
            }  
            Button(onClick = {  
                count--  
                logMessages = logMessages + "Clicked \"-\": count is $count"  
            }) {  
                Text("-")  
            }  
            Button(onClick = {  
                logMessages = logMessages + "Reset count from $count to 0"                count = 0  
            }) {  
                Text("Reset")  
            }  
        }  
        Spacer(modifier = Modifier.height(24.dp))  
  
        Text("Log of actions", style = MaterialTheme.typography.titleMedium)  
        LogList(logMessages)  
    }  
}

@Composable  
@Suppress("FunctionName")  
fun LogList(messages: List<String>) {  
    LazyColumn(  
        modifier = Modifier  
            .fillMaxWidth(),  
        verticalArrangement = Arrangement.spacedBy(4.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        items(messages) { msg ->  
            Text(  
                text = msg,  
                style = MaterialTheme.typography.bodyMedium  
            )  
        }  
    }}
```

#### Как это работает

1. **`SimpleUdfExample`** хранит всё состояние:
    
    - `count` (счётчик)
        
    - `logMessages` (список строк для «журнала»)
        
2. Когда пользователь нажимает любую из трёх кнопок, мы **меняем** `count` и **дописываем** новое сообщение в `logMessages`. Это и есть «событие» (Intent), которое идёт «вверх» из UI.
    
3. Compose следит за `count` и `logMessages`: после каждой их модификации происходит `recompose`:
    
    - Обновляется строка «Текущий счёт: $count».
        
    - Компонент `LogList` пересоздаёт свой `LazyColumn`, показывая все элементы списка.
        
4. **`LogList`** — это полностью **stateless**:
    
    - Получает сверху `messages: List<String>`.
        
    - Рисует `LazyColumn { items(messages) { … } }`.
        
    - Никаких `remember`, `state` или `collectAsState` внутри себя.
        

Таким образом, поток явно **однонаправленный**:

1. Родительский Compose (в данном случае `SimpleUdfExample`) **хранит состояние**.
    
2. UI (Text, Button, LogList) **читает** из этого состояния.
    
3. При клике на кнопки мы **генерируем событие** (увеличить/уменьшить/сбросить), которое вызывает функции, меняющие `count` и `logMessages`.
    
4. Compose «услышал» изменившиеся `State` и **перерисовал** те места, где они использовались.
    

Никаких ViewModel, Flow и прочих «архитектурных слоёв» здесь нет — только базовый `rememberSaveable` и чистые переиспользуемые composable-функции. Это полностью демонстрирует **unidirectional data flow** без лишних абстракций.

## 3. Шаблоны архитектур с UDF

### 3.1. MVI (Model–View–Intent)

1. **Intent** (событие от UI, `Action`) →
    
2. **ViewModel/Reducer** (меняет состояние, преобразует Intent в новый State) →
    
3. **State** →
    
4. **UI** (читает State, отображает данные).
    

В Compose часто называют это **Single Source of Truth**: единый источник правды — StateFlow/MutableState, с которыми работает весь UI.

### 3.2. MVVM с UDF

- **VM хранит State** (обычно через `StateFlow`, `LiveData` или `mutableStateOf`).
    
- **UI слушает State** через `collectAsState()` или `observeAsState()`.
    
- **UI шлёт события** (клики, ввод) обратно в VM через методы.
    
- **VM обновляет State**, UI пересобирается.

## 4. Основные преимущества и рекомендации

1. **Стабильность и читабельность**
    
    - Ваши composable-функции становятся **stateless** (не хранят собственное состояние, а получают его извне).
        
    - Легко проследить, что именно и когда изменяется.
        
2. **Генерация действий**
    
    - UI-компонент при клике/вводе порождает **Intent/Action**, который передается ViewModel.
        
    - Никаких «магических» вызовов `mutableStateOf` внутри composable (за исключением локальных вспомогательных переменных).
        
3. **Простота тестирования**
    
    - Тесты ViewModel: отправляете Intent, проверяете, какой State получился.
        
    - Тесты UI: задаёте State для данного screen, проверяете, что он отрисовался правильно (с помощью Compose Testing API).
        
4. **Отслеживание истории изменений (Time Travel Debugging)**
    
    - Инструменты Compose позволяют шагать назад/вперед по состояниям приложения, видеть, как UI реагирует на каждое изменение State.
        
5. **Избегайте Side Effect в UI**  
    Вся «побочная» логика (загрузка из сети, запись в БД, навигация) должна быть вынесена во ViewModel/Use Case и запускаться через `LaunchedEffect` / `DisposableEffect` лишь для привязки к жизненному циклу UI, но не для хранения данных.

## 5. Краткий чек-лист при работе с UDF в Compose

1. **Определите State data class** (или набор `StateFlow`/`LiveData`/`mutableStateOf`), который описывает всё, что нужно экрану.
    
2. **В ViewModel**:
    
    - Держите State в виде `MutableStateFlow<MyUiState>`.
        
    - Создавайте методы (Intent/Action), которые будут менять `_state.value`.
        
3. **В Composable**:
    
    - Подпишитесь на State (`collectAsState()` или `.value`).
        
    - Отображайте UI на основе этого `state`.
        
    - При кликах/событиях вызывайте методы ViewModel (Intent).
        
4. **Используйте `remember`, `derivedStateOf`, `LaunchedEffect`, `SideEffect`** — только когда это действительно нужно, и не храните бизнес-логику в composable.
    
5. **Всегда давайте ключи (`key = { … }`) в `Lazy*` списках**, чтобы Compose мог оптимально обновлять только нужные элементы.

