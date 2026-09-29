# 📝 Notes API

REST API сервис для управления заметками с тегами, построенный на Flask и SQLAlchemy. Поддерживает полный CRUD, поиск, фильтрацию по тегам, пагинацию и быстрое развёртывание через Docker.

**🌐 Демо-версия:** [https://notes-api-6pa2.onrender.com/api/](https://notes-api-6pa2.onrender.com/api/)

---

## 🚀 Стек технологий

* **Python 3.11** + **Flask 3.0**
* **SQLAlchemy ORM** + **SQLite** (для разработки) / **PostgreSQL** (для продакшна)
* **pytest** + **pytest-cov** для unit-тестирования и проверки покрытия
* **Docker** + **docker-compose** для контейнеризации
* Хостинг и развёртывание на платформе **[Render](https://render.com)**

---

## 📂 Структура проекта

```text
notes-api/
├── app/
│   ├── __init__.py      # Фабрика приложения (Application Factory)
│   ├── database.py      # Инициализация экземпляра SQLAlchemy
│   ├── models.py        # Данные и ORM-модели (Note, Tag)
│   └── routes.py        # Обработчики эндпоинтов API
├── tests/
│   ├── conftest.py      # Фикстуры и конфигурация pytest
│   └── test_api.py      # Набор unit-тестов (21 тест)
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── .env.example
├── .gitignore
└── run.py               # Точка входа для запуска приложения
```

---

## 💻 Локальная установка и запуск

### Классический запуск в виртуальном окружении

1. **Клонируйте репозиторий и перейдите в папку проекта:**
   ```bash
   git clone https://github.com/alesyazao/notes-api.git
   cd notes-api
   ```

2. **Создайте и активируйте виртуальное окружение:**
   ```bash
   python -m venv .venv
   # Для Linux / macOS:
   source .venv/bin/activate
   # Для Windows (Command Prompt):
   .venv\Scripts\activate.bat
   # Для Windows (PowerShell):
   .venv\Scripts\Activate.ps1
   ```

3. **Установите все необходимые зависимости:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Настройте переменные окружения:**
   ```bash
   cp .env.example .env
   ```
   *Откройте файл `.env` и укажите как минимум ваш секретный ключ (`SECRET_KEY`).*

5. **Запустите сервер для разработки:**
   ```bash
   python run.py
   ```
   *Приложение станет доступно по адресу:* `http://localhost:5000/api`

---

### Запуск с помощью Docker

Если у вас установлен Docker, вы можете собрать и запустить проект (веб-сервер вместе с базой данных PostgreSQL) одной командой:

```bash
# Собрать образы и запустить контейнеры в интерактивном режиме
docker-compose up --build

# Запустить контейнеры в фоновом режиме (detached)
docker-compose up -d --build

# Остановить работу контейнеров и удалить их
docker-compose down
```

---

## 🧪 Запуск тестов

Для проверки работоспособности кода используются unit-тесты:

```bash
# Запустить стандартный прогон всех тестов
pytest

# Запустить тесты с выводом подробной информации
pytest -v

# Запустить тесты и сформировать отчёт о покрытии кода (coverage)
pytest --cov=app --cov-report=term-missing
```

---

## 📖 Справочник API

* **Базовый URL (локально):** `http://localhost:5000/api`
* **Базовый URL (продакшн):** `https://notes-api-6pa2.onrender.com/api`

### Основные эндпоинты

| Метод | URL | Описание |
| :--- | :--- | :--- |
| **GET** | `/` | Общая информация о сервисе и его версии |
| **GET** | `/health` | Проверка здоровья сервиса (Health Check) |
| **GET** | `/notes` | Получить список заметок (с поддержкой поиска, фильтров и пагинации) |
| **GET** | `/notes/<id>` | Получить подробную информацию об одной заметке |
| **POST** | `/notes` | Создать новую заметку |
| **PUT** | `/notes/<id>` | Полностью или частично обновить существующую заметку |
| **DELETE** | `/notes/<id>` | Удалить заметку из базы данных |
| **GET** | `/tags` | Получить список всех существующих тегов |

### Параметры фильтрации и пагинации для `GET /notes`

* `q` (string) — Поисковый запрос для поиска подстроки в заголовках (`title`) и тексте (`content`) заметок.
* `tag` (string) — Фильтрация заметок по точному имени тега.
* `sort` (string) — Поле для сортировки. Доступные варианты: `created_at` (по умолчанию), `updated_at`, `title`.
* `order` (string) — Направление сортировки: `desc` (по убыванию, по умолчанию) или `asc` (по возрастанию).
* `page` (integer) — Номер запрашиваемой страницы (по умолчанию `1`).
* `per_page` (integer) — Количество заметок на одной странице (по умолчанию `10`, максимум `100`).

---

## 📡 Примеры cURL запросов

**Проверить статус сервиса:**
```bash
curl http://localhost:5000/api/health
```

**Получить список всех заметок:**
```bash
curl http://localhost:5000/api/notes
```

**Запрос с пагинацией (страница 1, 5 элементов):**
```bash
curl "http://localhost:5000/api/notes?page=1&per_page=5"
```

**Поиск по ключевому слову с сортировкой по алфавиту:**
```bash
curl "http://localhost:5000/api/notes?q=flask&sort=title&order=asc"
```

**Фильтрация заметок по конкретному тегу:**
```bash
curl "http://localhost:5000/api/notes?tag=python"
```

**Получить заметку по ID:**
```bash
curl http://localhost:5000/api/notes/1
```

**Создать новую заметку:**
```bash
curl -X POST http://localhost:5000/api/notes \
  -H "Content-Type: application/json" \
  -d '{"title": "Моя первая заметка", "content": "Привет, Notes API!", "tags": ["python", "flask"]}'
```

**Обновить существующую заметку:**
```bash
curl -X PUT http://localhost:5000/api/notes/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Обновлённый заголовок", "tags": ["обновлено"]}'
```

**Удалить заметку по ID:**
```bash
curl -X DELETE http://localhost:5000/api/notes/1
```

**Получить список всех уникальных тегов:**
```bash
curl http://localhost:5000/api/tags
```

---

## 📄 Пример структуры JSON-ответа (`GET /api/notes`)

```json
{
  "notes": [
    {
      "id": 1,
      "title": "Моя первая заметка",
      "content": "Привет, Notes API!",
      "tags": [
        {"id": 1, "name": "python"},
        {"id": 2, "name": "flask"}
      ],
      "created_at": "2025-06-15T10:30:00.000000",
      "updated_at": "2025-06-15T10:30:00.000000"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10,
  "pages": 1
}
```

