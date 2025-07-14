В Jetpack Compose `Image` — это базовый элемент UI, предназначенный для отображения изображений. Он работает аналогично `ImageView` в классическом Android View, но полностью адаптирован под декларативный подход Compose.

---

## 📦 Импорт

```kotlin
import androidx.compose.foundation.Image
import androidx.compose.runtime.Composable
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.Modifier
```

---

## 🖼️ Базовый пример (ресурс из drawable)

```kotlin
@Composable
fun MyImage() {
    Image(
        painter = painterResource(id = R.drawable.my_image),
        contentDescription = "Описание изображения"
    )
}
```

---

## 🧭 Параметры

|Параметр|Описание|
|---|---|
|`painter`|Указывает, откуда брать изображение (ресурс, URL, bitmap и т.д.)|
|`contentDescription`|Текст для accessibility (можно `null`, если неважно)|
|`modifier`|Задает размер, отступы, форму и т.д.|
|`contentScale`|Определяет, как изображение масштабируется внутри контейнера|

---

## 🎨 Пример с `modifier` и `contentScale`

```kotlin
Image(
    painter = painterResource(id = R.drawable.landscape),
    contentDescription = null,
    modifier = Modifier
        .fillMaxWidth()
        .height(200.dp),
    contentScale = ContentScale.Crop
)
```

### Возможные значения `ContentScale`:

- `FillBounds` – растягивает изображение до границ
    
- `Crop` – обрезает изображение, чтобы заполнить контейнер
    
- `Fit` – сохраняет пропорции, помещая изображение внутрь
    
- `Inside` – как `Fit`, но не увеличивает изображение
    

---

## 🌐 Загрузка из интернета (с помощью Coil)

Добавь зависимость:

```kotlin
implementation("io.coil-kt:coil-compose:2.5.0") // проверь последнюю версию
```

```kotlin
import coil.compose.AsyncImage

@Composable
fun NetworkImage() {
    AsyncImage(
        model = "https://example.com/image.jpg",
        contentDescription = "Сетевое изображение",
        modifier = Modifier.size(128.dp)
    )
}
```

---

## 🧪 Частые ошибки

- Не забывай `contentDescription` — это важно для доступности.
    
- Убедись, что `painterResource` ссылается на реально существующий ресурс.
    
- Для сетевых изображений обязательно добавляй зависимости и интернет-пермиссию в `AndroidManifest.xml`.
    
