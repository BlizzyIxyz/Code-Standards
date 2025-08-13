# Руководство по стилю кода для Unity C#

Цель: **читаемый, предсказуемый код**, где имена и оформление сразу дают контекст.

---

## 1. Общие принципы
- **Читаемость важнее краткости.** Лучше немного больше букв в имени, чем читать код с подглядыванием в контекст.  
- **Единообразие.** Все должны соблюдать одинаковые правила форматирования и имен.  
- **Следуем .NET/C# и Unity.** Но адаптируем под Unity-проекты (атрибуты, сериализация и т.п.).  
- **Имена должны передавать контекст.** Не `Loader`, а `PlayerProfileLoaderFromRemote`.  
- **Минимум скрытых побочных эффектов.** Имя метода должно давать представление о побочных эффектах.

---

## 2. Форматирование кода

### 2.1 Отступы и пробелы
- Отступы — 4 пробела, без табов.  
- Пробелы вокруг операторов и после запятой: `int x = 5;`, `Foo(bar, baz)`.  
- Одна пустая строка между методами/свойствами.  
- Пустая строка перед `#region` и после `#endregion`.  
- Пустые строки для логических блоков.

**Пример:**
```csharp
public class Example
{
    private int _value;

    public void Do()
    {
        var a = 1;

        if (condition)
        {
            DoSomething();
        }
    }

    #region Properties

    public int Value => _value;

    #endregion
}
```

### 2.2 Длина строки
- Максимум ~120 символов.

Переносим длинные аргументы и выражения:
```csharp
var result = SomeLongMethodName(
    firstArgument,
    secondArgument,
    thirdArgument
);

if (firstCondition && secondCondition 
    || thirdCondition)
{
    // ...
}
```
### 2.3 Скобки и переносы
- Открывающая фигурная скобка на той же строке.
- Закрывающая — на отдельной строке.

```csharp
if (condition)
{
    // ...
}
else
{
    // ...
}
```

---

## 3. Наименование
### 3.1 Базовые соглашения
- PascalCase: классы, методы, свойства, события, `public` поля.
- camelCase: параметры методов, локальные переменные.
- _camelCase: `pirvate` и `protected` поля.

### 3.2 Префиксы и суффиксы

| Категория / Тип             | Префикс / Суффикс            | Пример имени                     | Пояснение |
|------------------------------|------------------------------|---------------------------------|-----------|
| **Интерфейсы**               | Префикс `I`                 | `IService`, `IDamageable`       | Все интерфейсы должны начинаться с `I` |
| **Асинхронные методы**       | Суффикс `Async`              | `LoadAsync`, `SavePlayerAsync`  | Метод возвращает `Task` / `UniTask` |
| **Boolean-переменные**       | Префикс `Is` / `Has` / `Can` | `IsActive`, `HasAmmo`, `CanInteract` | Ясно читается как true/false |
| **Фабрики**                   | Суффикс `Factory`            | `EnemyFactory`, `ProjectileFactory` | Класс создаёт объекты |
| **Провайдеры / Сервисы**      | Суффикс `Provider` / `Service` | `AudioService`, `DataProvider` | Класс предоставляет данные или доступ к ресурсам |
| **Менеджеры / Координаторы**  | Суффикс `Manager`            | `GameManager`, `InputManager`   | Координирует подсистемы; использовать только при необходимости |
| **Контроллеры поведения**     | Суффикс `Controller`         | `PlayerController`, `EnemyController` | Управляет логикой поведения объекта |
| **Репозитории / Кеши**       | Суффикс `Repository` / `Cache` | `PlayerRepository`, `ScoreCache` | CRUD или кэш данных |
| **Обработчики / Хендлеры**   | Суффикс `Handler` / `Processor` | `InputHandler`, `EventProcessor` | Обрабатывает события, команды или сообщения |
| **ScriptableObject**          | Суффикс `Config` / `Settings` | `PlayerConfig`, `GameSettings` | Настройки через ScriptableObject |
| **Пулы объектов**             | Суффикс `Pool`               | `BulletPool`, `EnemyPool`       | Пул объектов для повторного использования |
| **DTO / Data Transfer Objects** | Суффикс `Dto`                | `PlayerDto`, `LevelDto`         | Структуры для переносимых данных / сериализации |
| **Тестовые двойники**         | Суффикс `Stub` / `Mock` / `Fake` | `PlayerStub`, `AudioMock`       | Используется в unit-тестах |
| **События**                   | Префикс `On` /  Без префикса             | `OnDeath`, `DamageTaken`      | Для `UnityEvent`; C# события могут быть без префикса |
| **Абстрактные классы**        | Суффикс `Base`               | `EnemyBase`, `CharacterBase`    | Абстрактные классы, от которых наследуются конкретные реализации |
| **Пулы объектов**          | `BulletPool`, `EnemyPool` | Пул объектов для повторного использования |
| **DTO / Data Transfer**     | `PlayerDto`                | Структуры для переносимых данных / сериализации |
| **Тестовые двойники**      | `Stub`, `Mock`, `Fake`     | Для unit-тестов, отделяет реальные объекты от тестовых |
| **События**                | `OnDeath`, `HealthChanged` | Начинаются с `On` для `UnityEvent`; C# события могут без префикса |

**Примеры:**
- Плохо: `Loader`
- Хорошо: `LocalLevelDataLoader`, `RemoteUserProfileLoaderAsync`

### 3.3 Константы и readonly
- `const` UPPER_CASE (`MAX_HEALTH`, `DEFAULT_SPEED`).

### 3.4 Именование событий
- C# event: `HealthChanged` или `OnHealthChanged`
- Если есть данные: `PlayerDamaged{EventArgs}`

## 4. Организация файлов и классов
### 4.1 Порядок в файле (MonoBehaviour / класс)
- **`using`**
- **`Namespace`**
- **Публичный класс** (имя файла = имя класса)
- **Порядок в классе:**
- `#region Serialized Fields`
- `#region Public Properties`
- `#region Events`
- `#region Unity Messages` (`Awake`, `Start`, `Update`, `FixedUpdate` — в этом порядке)
- `#region Public Methods`
- `#region Protected Methods`
- `#region Private Methods`
- `#region Helper Types`

### 4.2 `Serialized` поля
- Используй `private` + `[SerializeField]` вместо `public`.
- Группируй с `[Header("...")]` и `[Tooltip("...")]`.

**Пример:**

```csharp
[Header("Movement")]
[Tooltip("Maximum speed of the player")]
[SerializeField, Min(0f)] private float _maxSpeed = 5f;
```
### 4.3 Regions
- Разумно используйте `#region` для разделения больших блоков, но не прячьте чрезмерно код.

5. Комментирование и документация
5.1 XML-документация
Для публичного API используйте /// <summary>, param, returns.

Коротко: зачем нужен метод, какие побочные эффекты, исключения.

### 5.2 Внутренние комментарии
- Комментируйте почему, а не что. Код должен показывать, что происходит; комментарий — почему такой выбор.
- Помечайте `// TODO:` с JIRA/номер таска, чтобы потом не потерялось.

**Плохой комментарий:**
```csharp
i++; // увеличиваем i
```
**Хороший:**
```csharp
// Используем A* потому что карты динамические и нужны эвристики по манхэттену
FindPathWithAStar();
```

## 6. Unity-специфичные правила
### 6.1 Кэширование компонент
- Кэшируйте компоненты в `Awake`/`OnEnable`.

```csharp
private Rigidbody _rigidbody;

private void Awake()
{
    _rigidbody = GetComponent<Rigidbody>();
}
```

### 6.2 RequireComponent
- Добавляйте `[RequireComponent(typeof(...))]`, если класс зависит от компонента.

## 7. Оптимизация (стиль, но с вниманием к производительности)
### 7.1 Аллокации
- Не аллоцируйте в `Update`: избегайте `LINQ`, `new` внутри `Update`, строковых конкатенаций.
- Используйте объектные пулы вместо частого `Instantiate`/`Destroy`.

### 7.2 GetComponent
- Минимизируйте вызовы `GetComponent` и кешируйте ссылки.

### 7.3 LINQ
- `LINQ` — читаемый и удобный, но не в hot-path (`Update`/`FixedUpdate`). В критичных местах используйте циклы.

## 8. Обработка ошибок и проверки
### 8.1 `Null`-проверки
- Проверяйте ссылки в публичных методах, логируйте или бросайте исключение в невосстановимых случаях.

```csharp
public void UseWeapon(Weapon weapon)
{
    if (weapon == null)
    {
        Debug.LogError("Weapon is not assigned!");
        return;
    }
}
```
### 8.2 Валидные входные данные
- Для публичных API — проверка параметров (`ArgumentNullException`, `ArgumentOutOfRangeException`) вместо тихого игнорирования.

## 9. Примеры хороших и плохих практик (конкретика)
**Плохо**
```csharp
public class Manager { /* много методов */ }
public int cnt;
public void Do() { /* не ясно */ }
```

**Хорошо**
```csharp
public class EnemySpawnController : MonoBehaviour
{
    [SerializeField] private GameObject _enemyPrefab;
    [SerializeField] private int _initialSpawnCount;

    public void SpawnAt(Vector3 position)
    {
        // явное и понятное имя, контекст в классе и методе
    }
}
```

**Асинхронный пример**
```csharp
public async UniTask<PlayerProfile> LoadPlayerProfileAsync(string userId)
{
    if (string.IsNullOrEmpty(userId))
        throw new ArgumentException(nameof(userId));

    var json = await _remoteService.GetJsonAsync(userId);
    return JsonUtility.FromJson<PlayerProfile>(json);
}
```
