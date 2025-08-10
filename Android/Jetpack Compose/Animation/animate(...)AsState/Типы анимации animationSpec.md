В **Jetpack Compose** есть несколько основных типов анимаций, которые задаются через параметр `animationSpec` (например, в `animateDpAsState`, `animateColorAsState`, `updateTransition`).  
Самые часто используемые — это **`tween()`**, **`spring()`** и **`keyframes()`**.

---

## 1. `tween()` — _интерполяция по времени_

**Когда использовать:** когда нужна плавная анимация за фиксированное время.

```kotlin
tween(
    durationMillis = 1000,       // длительность анимации
    delayMillis = 200,           // задержка перед стартом
    easing = LinearOutSlowInEasing // кривая ускорения/замедления
)
```

🔹 **Особенности:**

- Движение линейное или по кривой easing.
    
- Полезно для предсказуемых, одноразовых анимаций.
    
- Всегда занимает одинаковое время, независимо от расстояния между начальным и конечным значением.
    

**Пример:**

```kotlin
val size by animateDpAsState(
    targetValue = 200.dp,
    animationSpec = tween(1000, easing = FastOutSlowInEasing)
)
```

---

## 2. `spring()` — _пружинная анимация_

**Когда использовать:** когда нужен естественный эффект пружины с колебаниями.

```kotlin
spring(
    dampingRatio = Spring.DampingRatioMediumBouncy, // упругость
    stiffness = Spring.StiffnessLow                 // жёсткость
)
```

🔹 **Особенности:**

- Нет фиксированной длительности — анимация длится, пока пружина не "успокоится".
    
- Подходит для UI-эффектов с "отскоком".
    
- Можно имитировать физические свойства.
    

**Пример:**

```kotlin
val offset by animateDpAsState(
    targetValue = 300.dp,
    animationSpec = spring(dampingRatio = 0.5f)
)
```

---

## 3. `keyframes()` — _анимация по ключевым кадрам_

**Когда использовать:** когда нужно сделать сложную анимацию с несколькими промежуточными состояниями.

```kotlin
keyframes {
    durationMillis = 1000
    0.dp at 0 with LinearEasing
    150.dp at 500 with FastOutSlowInEasing
    100.dp at 800
}
```

🔹 **Особенности:**

- Можно задать несколько контрольных точек (`at <time>`).
    
- Каждая точка может иметь свою кривую движения.
    
- Отлично подходит для сложных сценариев (например, bounce без физики).
    

**Пример:**

```kotlin
val size by animateDpAsState(
    targetValue = 200.dp,
    animationSpec = keyframes {
        durationMillis = 1000
        100.dp at 0
        250.dp at 500
        200.dp at 1000
    }
)
```

---

## Быстрое сравнение

|Тип|Когда использовать|Длительность|Реализм движения|
|---|---|---|---|
|**`tween`**|Плавное изменение за фиксированное время|фиксированная|искусственная|
|**`spring`**|Естественные колебания/отскоки|автоматическая|физическая|
|**`keyframes`**|Сложные многоэтапные анимации|фиксированная|искусственная, но гибкая|
[[repeatable и infiniteRepeatable]]