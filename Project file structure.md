# Project File Structure

<pre>
Assets/  
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
├─ Editor/                   # Скрипты, работающие только в редакторе Unity  
├─ Art/                      # Общая папка для всех графических и аудио ассетов  
│  ├─ Models/                # 3D-модели и FBX  
│  ├─ Sprites/               # 2D-спрайты  
│  └─ Audio/                 # Звуковые клипы  
├─ Prefabs/                  # Префабы проекта  
│  ├─ UI/                     # Панели, кнопки  
│  ├─ Gameplay/  
│  │  ├─ Items/  
│  │  └─ Environment/  
│  └─ Characters/  
├─ Settings/                 # Конфиги, ScriptableObjects  
├─ Tests/                    # Unit-тесты и интеграционные тесты  
└─ Resources/                # Ассеты для Resources.Load  
</pre>
