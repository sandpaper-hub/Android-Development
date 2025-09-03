## 🔹 Что такое `AndroidViewModel`

`AndroidViewModel` — это наследник `ViewModel`, у которого в конструкторе **обязательно есть `Application`**:

```kotlin
class MyViewModel(application: Application) : AndroidViewModel(application) {
    fun getAppContext(): Context = getApplication()
}
```

То есть у него есть метод `getApplication<T>()`, через который можно получить **application context**.

---

## 🔹 Отличие от `ViewModel`

||`ViewModel`|`AndroidViewModel`|
|---|---|---|
|Доступ к `Context`|❌ нет|✅ есть через `getApplication()`|
|Тестируемость|Легче, так как нет Android-зависимостей|Сложнее, так как завязан на `Application`|
|Использование|99% случаев, хранение состояния и логики|Только если **нужен контекст приложения**|

---

## 🔹 Когда использовать `AndroidViewModel`

👉 **Нужен только тогда, когда во ViewModel требуется application context**, например:

- Инициализация `Room.databaseBuilder(context, ...)`.
    
- Работа с `SharedPreferences` напрямую.
    
- Доступ к ресурсам (`getString(R.string.some_text)`), если не хочешь прокидывать их через DI.
    
- Получение системных сервисов (`context.getSystemService(...)`).
    

---

## 🔹 Пример использования

### C `ViewModel`

```kotlin
class UserViewModel(private val repo: UserRepository) : ViewModel() {
    // работает только с данными, без контекста
}
```

### C `AndroidViewModel`

```kotlin
class SettingsViewModel(application: Application) : AndroidViewModel(application) {
    private val prefs = application.getSharedPreferences("settings", Context.MODE_PRIVATE)

    fun saveValue(key: String, value: String) {
        prefs.edit().putString(key, value).apply()
    }
}
```

---

## 🔹 Почему почти всегда лучше обычный `ViewModel`

- `AndroidViewModel` завязывает бизнес-логику на Android-фреймворк → хуже тестируется.
    
- Если используешь **DI (Hilt/Koin/Dagger)**, контекст можно передавать через зависимости, и `AndroidViewModel` не нужен.
    

Google сам рекомендует использовать `AndroidViewModel` **только в крайних случаях**.

---

✅ **Итог**:

- Используй **`ViewModel`** по умолчанию.
    
- Если во ViewModel нужен именно **application context** (БД, SharedPreferences, ресурсы) и ты не используешь DI → тогда берём **`AndroidViewModel`**.