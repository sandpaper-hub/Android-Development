В Jetpack Compose `Box` — это самый простой контейнер для компоновки: он «накладывает» (стэкует) своих потомков друг на друга по Z‑оси. Вот его ключевые особенности и возможности:

---

## 1. Простая структура

```kotlin
@Composable
fun GreetingBox() {
    Box(modifier = Modifier.size(200.dp).background(Color.LightGray)) {
        Text("Подложка", modifier = Modifier.align(Alignment.TopStart))
        Icon(Icons.Default.Favorite, contentDescription = null, modifier = Modifier.align(Alignment.Center))
        Text("Наложенный текст", modifier = Modifier.align(Alignment.BottomEnd))
    }
}
```

- Все три потомка отрисуются в одном и том же прямоугольнике `Box` размером 200×200 dp, но с разными позициями благодаря `Modifier.align(…)`.
    
- Последний в порядке вызова отрисуется сверху (на Z‑оси).
    

---

## 2. Позиционирование — `BoxScope.align`

- Внутри `Box` вы оперируете интерфейсом `BoxScope`.
    
- Для каждого потомка доступен модификатор `Modifier.align(Alignment)`, где `Alignment` может быть любым из:
    
    - `Alignment.TopStart`, `TopCenter`, `TopEnd`
        
    - `CenterStart`, `Center`, `CenterEnd`
        
    - `BottomStart`, `BottomCenter`, `BottomEnd`
        

---

## 3. `contentAlignment` — выравнивание по умолчанию

Если вы хотите, чтобы ВСЕ дети по умолчанию были выровнены одинаково, можно задать это сразу:

```kotlin
Box(
  modifier = Modifier.fillMaxSize(),
  contentAlignment = Alignment.Center  // все дети по центру
) {
  Text("Центрированный текст")
  Icon(Icons.Default.Star, contentDescription = null)
}
```

---

## 4. Размеры и отступы

`Box` сам по себе не задаёт размер — он растягивается в соответствии с модификаторами:

- `Modifier.wrapContentSize()` (по умолчанию)
    
- `Modifier.fillMaxSize()`, `fillMaxWidth()`, `fillMaxHeight()`
    
- `Modifier.size()`, `width()`, `height()`
    
- `padding()` для внутренних отступов (между границами Box и детьми)
    

---

## 5. Стек слоёв и Z‑порядок

- Порядок отрисовки детей соответствует порядку объявления: первый — внизу, последний — сверху.
    
- Это удобно для наложения полупрозрачных шейдов, плавающих кнопок и индикаторов.
    

---

## 6. BoxWithConstraints

Если вам нужно адаптировать содержимое под фактический размер контейнера, используйте `BoxWithConstraints`:

```kotlin
BoxWithConstraints(modifier = Modifier.fillMaxSize()) {
    if (maxWidth < 300.dp) {
        Text("Узкий экран")
    } else {
        Text("Широкий экран")
    }
}
```

- Внутри `BoxWithConstraints` доступны свойства `maxWidth`, `maxHeight`, `minWidth`, `minHeight`.
    

---

## 7. Применение в реальных сценариях

- **Оверлеи**: поместить затемняющий полупрозрачный слой поверх основного UI.
    
- **Позиционирование кнопки «плюс»**: `FloatingActionButton` можно разместить в правом нижнем углу, обернув в `Box` с `contentAlignment = BottomEnd`.
    
- **Кастомные тултипы**: всплывающие подсказки поверх элементов.
    
- **Фон + контент**: вставить картинку на задний план и наложить текст/иконки.
    

---

- `Box` — это базовый «фрейм» для наложения элементов.
    
- `Modifier.align` и `contentAlignment` управляют позицией потомков.
    
- `BoxWithConstraints` дает доступ к ограничениям по размеру.
    

Используйте `Box` всегда, когда вам нужно «сложить» несколько слоев UI друг на друга и гибко позиционировать их в одном прямоугольнике.