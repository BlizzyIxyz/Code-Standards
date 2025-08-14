# Паттерны проектирования в Unity

## 1. Стратегия (Strategy)
**Назначение:** Позволяет выбирать алгоритм во время выполнения без изменения клиента.  
**Примеры использования:**
- Разные варианты поведения врагов (`Unit`).  
- Смена системы управления (клавиатура, контроллер и т.д.).

**Пример кода:**
```csharp
public interface IMovementStrategy 
{
    void Move(Transform transform);
}

public class WalkStrategy : IMovementStrategy 
{
    public void Move(Transform transform) => transform.Translate(Vector3.forward * 1f);
}

public class RunStrategy : IMovementStrategy 
{
    public void Move(Transform transform) => transform.Translate(Vector3.forward * 2f);
}

public class PlayerMovement : MonoBehaviour 
{
    private IMovementStrategy _currentStrategy;

    public void SetStrategy(IMovementStrategy strategy) => _currentStrategy = strategy;
    
    private void Update() 
    {
        _currentStrategy?.Move(transform);
    }
}
```

---

## 2. Наблюдатель (Observer)
**Назначение:** Реализует события и уведомления между системами для уменьшения связанности.
**Примеры использования:**
- Система уведомлений о подборе предметов.
- Изменение количества здоровья.
- Глобальные события: смерть врага, завершение уровня.

**Пример кода:**
```csharp
public class GameEvents 
{
    public static event Action<int> OnScoreChanged;

    public static void NotifyScoreChanged(int score) => OnScoreChanged?.Invoke(score);
}

// Подписка:
public class ScoreDisplay : MonoBehaviour 
{
    private void OnEnable() => GameEvents.OnScoreChanged += UpdateScore;
    private void OnDisable() => GameEvents.OnScoreChanged -= UpdateScore;

    private void UpdateScore(int score) => Debug.Log($"New score: {score}");
}
```
Альтернатива: `[SerializeField] private UnityEvent OnEventName`;
Использовать осторожно — может усложнить поддержку кода.

---

## 3. Машина состояний (FSM)
**Назначение:** Управление поведением объекта через чёткие состояния и переходы.
**Примеры использования:**
- AI врагов: Патрулирование → Преследование → Атака.
- Анимации персонажа: Idle → Walk → Run → Jump.
- Логика игры: Меню → Геймплей → Пауза.

**Пример кода:**

```csharp
public interface IState 
{
    void Enter();
    void Update();
    void Exit();
}

public class StateMachine 
{
    private IState _currentState;

    public void ChangeState(IState newState) 
    {
        _currentState?.Exit();
        _currentState = newState;
        _currentState.Enter();
    }

    public void Update() => _currentState?.Update();
}

// Пример состояния:
public class PatrolState : IState 
{
    public void Enter() => Debug.Log("Начинаю патрулирование");
    public void Update() => Debug.Log("Патрулирую...");
    public void Exit() => Debug.Log("Заканчиваю патрулирование");
}
```
**Советы:**
- Для сложных FSM полезно составлять диаграммы состояний или отдельное окно в редакторе Unity.
- Можно использовать `ScriptableObject`-состояния для большей гибкости и переиспользуемости.

---

## 4. Адаптер (Adapter)
**Назначение:** Преобразует интерфейс одного класса в интерфейс, ожидаемый клиентом.
**Примеры использования:**
- Интеграция сторонних SDK с разными API.
- Работа с разными форматами данных (JSON ↔ XML).
- Адаптация устаревшего кода к новой системе.
- Унификация или связываени "несвязуемого"

**Пример кода:**
```csharp
// Старый интерфейс (используется в проекте)
public interface IOldSaveSystem 
{
    void SaveGame(string path);
}

// Новый интерфейс (например, SDK облачного сохранения)
public interface INewSaveSystem 
{
    void SaveToCloud(string data);
}

// Адаптер:
public class CloudSaveAdapter : IOldSaveSystem 
{
    private INewSaveSystem _cloudSaver;

    public CloudSaveAdapter(INewSaveSystem cloudSaver) 
    {
        _cloudSaver = cloudSaver;
    }

    public void SaveGame(string path)
    {
        string data = File.ReadAllText(path);
        _cloudSaver.SaveToCloud(data); // Адаптация вызова
    }
}
```

---

## 5. Event Bus (Шина событий)
**Назначение:** Централизованная система событий для уменьшения связанности кода.
**Примечание:** В большинстве проектов Unity используется Zenject.
**Примеры использования:**
- Глобальные уведомления (смерть игрока, завершение уровня).
- Связь между UI и геймплеем без прямых ссылок.
- Коммуникация между системами (Achievements ↔ Gameplay).

**Пример кода:**
```csharp
public static class EventBus 
{
    private static Dictionary<Type, Delegate> _events = new();

    public static void Subscribe<T>(Action<T> handler) 
    {
        _events.TryAdd(typeof(T), null);
        _events[typeof(T)] = (Action<T>)_events[typeof(T)] + handler;
    }

    public static void Publish<T>(T eventData) 
    {
        if (_events.TryGetValue(typeof(T), out var @event)) 
        {
            (@event as Action<T>)?.Invoke(eventData);
        }
    }
}

// Пример события:
public struct PlayerDiedEvent 
{
    public Vector3 DeathPosition;
}

// Подписка:
public class AchievementSystem 
{
    public AchievementSystem() 
    {
        EventBus.Subscribe<PlayerDiedEvent>(OnPlayerDied);
    }

    private void OnPlayerDied(PlayerDiedEvent e) 
    {
        UnlockAchievement("First Death");
    }
}

// Публикация:
public class PlayerHealth : MonoBehaviour 
{
    public void Die() 
    {
        EventBus.Publish(new PlayerDiedEvent { DeathPosition = transform.position });
    }
}
```
**Важно:** `EventBus` может превратиться в «мусорную свалку событий». Не злоупотребляйте и документируйте подписки.
