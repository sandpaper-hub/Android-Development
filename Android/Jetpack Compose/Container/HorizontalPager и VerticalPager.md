## 🌀 Что такое `HorizontalPager` и `VerticalPager`

Это компоненты-представления **страничной прокрутки**, пришедшие на замену `accompanist-pager`, и с версии `foundation:1.4.0` стали частью официальной библиотеки Compose.

Они позволяют отображать **страницы (page)** с чёткой привязкой к позиции, автоматически обеспечивая фиксацию, скролл между страницами и управление состоянием через `PagerState`.

---

## 🛠️ Основная концепция пагинации

В контексте Compose:

- **Пагинация** = отображение контента «по страницам» (page-based UI), где каждая страница — отдельный экран/элемент.
- В отличие от `LazyColumn`, здесь нет бесконечной прокрутки, а есть **позиционная навигация**: только одна (или часть одной) страницы видна на экране.
- `PagerState` управляет: текущей страницей, скроллированием и анимациями.

---

## 🧩 Конструктор `HorizontalPager` (аналогично `VerticalPager`)

```kotlin
HorizontalPager(
    state: PagerState,
    modifier: Modifier = Modifier,
    contentPadding: PaddingValues = PaddingValues(0.dp),
    pageSize: PageSize = PageSize.Fill,
    beyondViewportPageCount: Int = PagerDefaults.BeyondViewportPageCount,
    pageSpacing: Dp = 0.dp,
    verticalAlignment: Alignment.Vertical = Alignment.CenterVertically,
    flingBehavior: TargetedFlingBehavior = PagerDefaults.flingBehavior(state),
    userScrollEnabled: Boolean = true,
    reverseLayout: Boolean = false,
    key: ((Int) -> Any)? = null,
    pageNestedScrollConnection: NestedScrollConnection = PagerDefaults.pageNestedScrollConnection(state, Orientation.Horizontal),
    snapPosition: SnapPosition = SnapPosition.Start,
    pageContent: @Composable PagerScope.(page: Int) -> Unit
)
```

---

## 📖 Пояснение параметров

| Параметр | Значение |
|---------|----------|
| `state` | `PagerState`, содержит информацию о текущей странице, позицию прокрутки, scroll-методы. Без него `Pager` не работает. |
| `modifier` | Оформление самого пейджера: размеры, отступы и т.д. |
| `contentPadding` | Внутренние отступы — например, чтобы показать соседние страницы по краям. |
| `pageSize` | Размер каждой страницы: `PageSize.Fill` (на весь экран) или `PageSize.Fixed(300.dp)`. |
| `beyondViewportPageCount` | Кол-во страниц, рендерящихся вне видимой области (вперед/назад). Влияет на производительность. |
| `pageSpacing` | Расстояние между страницами. Полезно для визуального разделения. |
| `verticalAlignment` | Выравнивание контента внутри каждой страницы по вертикали. |
| `flingBehavior` | Как себя ведёт скролл при отпускании пальца — ускорение, инерция, "защелкивание". Обычно по умолчанию подходит. |
| `userScrollEnabled` | Если `false`, пользователь не сможет свайпать. Полезно для программного контроля. |
| `reverseLayout` | Меняет направление прокрутки (справа налево / снизу вверх). |
| `key` | Уникальный ключ страницы. Используется для сохранения состояния и анимаций при изменении данных. |
| `pageNestedScrollConnection` | Позволяет подключать вложенный скролл, например, для взаимодействия с `LazyColumn`. |
| `snapPosition` | Куда будет "прилепать" страница: `Start`, `Center`, `End`. Управляет выравниванием страницы при прокрутке. |
| `pageContent` | Контент страницы — Composable-функция с номером `page: Int`. |

---

## 🧠 Что такое `PagerState`

```kotlin
val pagerState = rememberPagerState(
    initialPage = 0,
    initialPageOffsetFraction = 0f,
    pageCount = { 5 } // Важно: функция, не просто число
)
```

`PagerState` — это класс, который:
- хранит текущую страницу (`currentPage`)
- позволяет **программно прокручивать** (`scrollToPage`, `animateScrollToPage`)
- отслеживает позицию анимации и оффсеты
- реагирует на изменения количества страниц

---

## 📊 Отличие от `LazyColumn`

| Feature | Pager | LazyColumn |
|--------|-------|------------|
| Позиционная фиксация | ✅ Да | ❌ Нет |
| Прокрутка с одной страницы на другую | ✅ Да | ❌ Нет |
| Используется для "onboarding", слайдеров, галерей | ✅ Да | ❌ Нет |
| Бесконечный список | ❌ (только фиксированные страницы) | ✅ Да |
| Управление страницей по индексу | ✅ Да | ❌ Нет |

---

## 🔄 Пример программной прокрутки

```kotlin
LaunchedEffect(Unit) {
    pagerState.animateScrollToPage(3)
}
```

---

## 📌 `VerticalPager` — то же самое

```kotlin
VerticalPager(
    state = pagerState,
    modifier = Modifier.fillMaxSize(),
    pageSize = PageSize.Fill,
    // остальные параметры аналогичны
) { page ->
    // Контент страницы
}
```

Отличие: ось прокрутки — вертикальная (`Orientation.Vertical`), используется чаще для экранов/шагов формы и презентаций.

---

## 📦 Когда использовать Pager

| Сценарий | HorizontalPager / VerticalPager |
|----------|-------------------------------|
| Onboarding / Введение | ✅ HorizontalPager |
| Галерея фото | ✅ HorizontalPager |
| Интерактивные шаги | ✅ VerticalPager |
| PDF просмотр | ✅ VerticalPager |
| Слайдер/Карусель | ✅ HorizontalPager с `PageSize.Fixed()` и `contentPadding` |
| Подкачка данных | 🚫 Лучше `LazyColumn` с ручной пагинацией |
## Пример
```kotlin
@Composable  
@Suppress("FunctionName")  
fun HorizontalPagerExample(modifier: Modifier) {  
    val pageCount = 5  
    val pagerState = rememberPagerState(initialPage = 0, pageCount = { pageCount })  
  
    HorizontalPager(  
        state = pagerState,  
        modifier = modifier.then(Modifier.fillMaxSize()),  
  
        pageSpacing = 16.dp,  
        pageSize = PageSize.Fill,  
        verticalAlignment = Alignment.CenterVertically,  
        reverseLayout = false,  
        key = { it },  
        snapPosition = SnapPosition.Start  
    ) { page ->  
        ConstraintLayout(  
            modifier = Modifier.fillMaxSize().background(  
                when (page % 3) {  
                    0 -> Color.Red  
                    1 -> Color.Green  
                    else -> Color.Blue  
                }  
            )        ) {  
            val text = createRef()  
  
            Text("Page #${page + 1}", fontSize = 28.sp, modifier = Modifier.constrainAs(text) {  
                centerHorizontallyTo(parent)  
                centerVerticallyTo(parent)  
            })  
        }  
    }}
```