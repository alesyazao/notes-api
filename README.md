# 📝 Notes API

REST API сервис для управления заметками с тегами, построен на Flask и SQLAlchemy. Поддерживает полный CRUD, поиск, фильтрацию по тегам, пагинацию и развёртывание через Docker.

**🌐 Демо:** https://notes-api-6pa2.onrender.com/api/

---

## Стек технологий

- **Python 3.11** + **Flask 3.0**
- **SQLAlchemy** ORM + **SQLite** (разработка) / **PostgreSQL** (продакшн)
- **pytest** для тестирования
- **Docker** + **docker-compose** для контейнеризации
- Развёрнуто на **[Render](https://render.com)**

---

## Структура проекта
notes-api/
├── app/
│ ├── init.py # фабрика приложения
│ ├── database.py # экземпляр SQLAlchemy
│ ├── models.py # модели Note, Tag
│ └── routes.py # все эндпоинты API
├── tests/
│ ├── conftest.py # фикстуры pytest
│ └── test_api.py # unit-тесты (21 тест)
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── .env.example
├── .gitignore
└── run.py # точка входа приложения

---

## Локальная установка

### 1. Клонируй репозиторий и создай виртуальное окружение

git clone https://github.com/alesyazao/notes-api.git
cd notes-api
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

2. Установи зависимости
pip install -r requirements.txt

2. Установи зависимости
pip install -r requirements.txt
3. Настрой переменные окружения

cp .env.example .env
# Отредактируй .env — минимум установи SECRET_KEY
4. Запусти сервер разработки

python run.py
API будет доступен по адресу http://localhost:5000.

Запуск через Docker

# Собрать и запустить web + postgres
docker-compose up --build

# Запустить в фоновом режиме
docker-compose up -d --build

# Остановить
docker-compose down
Запуск тестов

# Запустить все тесты
pytest

# С отчётом о покрытии
pytest --cov=app --cov-report=term-missing

# С подробным выводом
pytest -v
Справочник API
Базовый URL (локально): http://localhost:5000/api
Базовый URL (продакшн): https://notes-api-6pa2.onrender.com/api

Эндпоинты
Метод	URL	Описание
GET	/	Информация о сервисе
GET	/health	Проверка здоровья сервиса
GET	/notes	Получить список заметок (поиск, фильтр, пагинация)
GET	/notes/<id>	Получить одну заметку
POST	/notes	Создать новую заметку
PUT	/notes/<id>	Обновить заметку
DELETE	/notes/<id>	Удалить заметку
GET	/tags	Получить список всех тегов
Параметры запроса для GET /notes
Параметр	Тип	По умолчанию	Описание
q	string	—	Поиск в заголовке и содержимом
tag	string	—	Фильтр по имени тега
sort	string	created_at	Поле сортировки: created_at, updated_at, title
order	string	desc	asc или desc
page	integer	1	Номер страницы
per_page	integer	10	Элементов на странице (максимум 100)
Примеры curl-запросов
Проверка здоровья

curl http://localhost:5000/api/health
Получить все заметки

curl http://localhost:5000/api/notes
Получить заметки с пагинацией

curl "http://localhost:5000/api/notes?page=1&per_page=5"
Поиск заметок

curl "http://localhost:5000/api/notes?q=flask&sort=title&order=asc"
Фильтр заметок по тегу

curl "http://localhost:5000/api/notes?tag=python"
Получить одну заметку

curl http://localhost:5000/api/notes/1
Создать заметку

curl -X POST http://localhost:5000/api/notes \
  -H "Content-Type: application/json" \
  -d '{"title": "Моя первая заметка", "content": "Привет, Notes API!", "tags": ["python", "flask"]}'
Обновить заметку

curl -X PUT http://localhost:5000/api/notes/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Обновлённый заголовок", "tags": ["обновлено"]}'
Удалить заметку

curl -X DELETE http://localhost:5000/api/notes/1
Получить список всех тегов

curl http://localhost:5000/api/tags
Пример ответа
GET /api/notes

json
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


