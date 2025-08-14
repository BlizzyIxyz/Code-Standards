# Project File Structure

<pre>
Assets
├─ Scripts/                  # Весь код проекта
│  ├─ UI/                    # Логика UI (User Interface Layer)
│  ├─ Gameplay/              # Игровая логика (Domain Layer)
│  │  ├─ Items/              # Предметы, оружие, инвентарь
│  │  ├─ Environment/        # Окружение (двери, ловушки)
│  │  └─ Characters/         # Игрок, NPC, враги
│  └─ Infrastructure/        # Инфраструктурный код
│     ├─ Installers/         # Zenject Installers
│     ├─ Services/           # Сервисы (сохранение, звук)
│     ├─ EventBus/           # Шина событий
│     └─ Utilities/          # Утилиты, хелперы
├─ Models/                    # 3D-модели и FBX
├─ Prefabs/
│  ├─ UI/                     # Панели, кнопки
│  ├─ Gameplay/
│  │  ├─ Items/
│  │  └─ Environment/
│  └─ Characters/
├─ Settings/                  # Конфиги, ScriptableObjects
└─ Resources/                 # Ассеты для Resources.Load
</pre>
