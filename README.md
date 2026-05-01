# ITCSite — Фотоальбом на Django

> **A simple Django-based photo-album web application** — upload images with a title and description, browse them in a responsive Bootstrap gallery, and manage everything via a Jazzmin-styled admin panel.

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python)](https://www.python.org/)
[![Django](https://img.shields.io/badge/Django-4.2.1-green?logo=django)](https://www.djangoproject.com/)
[![Bootstrap](https://img.shields.io/badge/Bootstrap-5.2.3-purple?logo=bootstrap)](https://getbootstrap.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## Возможности

- 📸 **Галерея постов** — главная страница отображает все посты в адаптивной Bootstrap-сетке с изображением, описанием и датой публикации.
- ➕ **Добавление поста** — страница `/insert` позволяет загрузить изображение и заполнить заголовок с описанием.
- 🌗 **Переключение темы** — встроенный Bootstrap-переключатель светлой / тёмной / авто-темы.
- 🛡️ **Панель администратора** — управление постами (список, фильтрация по дате, поиск по заголовку, превью изображения) через Django Admin с темой Jazzmin.
- 🗄️ **SQLite «из коробки»** — база данных разворачивается одной командой миграции.

---

## Стек технологий

| Слой | Технология | Версия |
|------|-----------|--------|
| Backend | Python / Django | 3.10+ / 4.2.1 |
| ORM + DB | Django ORM / SQLite3 | — |
| Admin UI | django-jazzmin | 2.6.0 |
| Обработка изображений | Pillow | 9.5.0 |
| Переменные окружения | python-dotenv | 1.0.0 |
| Frontend | Bootstrap (CDN) | 5.2.3 |

---

## Структура проекта

```
dadada/
├── ITCSite/               # Конфигурация Django-проекта
│   ├── settings.py        # Настройки (SECRET_KEY из .env, БД, медиа, статика)
│   ├── urls.py            # Корневые URL: /admin/, /, /insert/
│   ├── wsgi.py
│   └── asgi.py
│
├── home/                  # Основное приложение
│   ├── models.py          # Модель Post (image, title, content, created_at, updated_at)
│   ├── views.py           # index (GET /) и insert_post (GET+POST /insert)
│   ├── urls.py            # URL-паттерны приложения
│   ├── forms.py           # PostForm (ModelForm для Post)
│   ├── admin.py           # PostAdmin с превью изображения
│   └── migrations/        # Миграции БД
│
├── templates/
│   ├── home/index.html    # Страница галереи
│   └── insert/index.html  # Форма добавления поста
│
├── jazzmin/               # Локальная копия django-jazzmin
├── media/                 # Загруженные пользователями файлы
├── manage.py              # Точка входа Django CLI
├── requirements.txt       # Зависимости Python
├── run.sh                 # Скрипт быстрого запуска
├── .env                   # Переменные окружения (не коммитить!)
└── .env.example           # Шаблон переменных окружения
```

---

## Быстрый старт

### Требования

- Python **3.10+**
- pip

### 1. Клонировать репозиторий

```bash
git clone https://github.com/Sent1nelX/dadada.git
cd dadada
```

### 2. Создать и активировать виртуальное окружение

```bash
python3 -m venv venv
source venv/bin/activate      # Linux / macOS
# venv\Scripts\activate       # Windows
```

### 3. Установить зависимости

```bash
pip install -r requirements.txt
```

### 4. Настроить переменные окружения

```bash
cp .env.example .env
# Откройте .env и задайте SECRET_KEY
```

### 5. Применить миграции и запустить сервер

```bash
# Вариант 1 — скрипт run.sh
bash run.sh

# Вариант 2 — вручную
python3 manage.py makemigrations
python3 manage.py migrate
python3 manage.py runserver 8000
```

Приложение доступно по адресу: **http://127.0.0.1:8000/**

### 6. Создать суперпользователя (для /admin/)

```bash
python3 manage.py createsuperuser
```

---

## Конфигурация / Переменные окружения

Настройки хранятся в файле `.env` в корне проекта. Пример — в `.env.example`.

| Переменная | Обязательна | Описание |
|-----------|:-----------:|---------|
| `SECRET_KEY` | ✅ | Секретный ключ Django для сессий и CSRF |

> ⚠️ Никогда не коммитьте реальный `SECRET_KEY` в публичный репозиторий. Для генерации нового ключа:
> ```bash
> python3 -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
> ```

### Прочие настройки (`ITCSite/settings.py`)

| Параметр | По умолчанию | Описание |
|---------|-------------|---------|
| `DEBUG` | `True` | Режим отладки — **отключить в продакшене** |
| `ALLOWED_HOSTS` | `[]` | Разрешённые хосты — заполнить для деплоя |
| `DATABASES` | SQLite (`db.sqlite3`) | База данных |
| `MEDIA_ROOT` | `<BASE_DIR>/media/` | Директория загрузок |
| `TIME_ZONE` | `UTC` | Часовой пояс |

---

## Разработка

```bash
# Создать миграцию после изменения моделей
python3 manage.py makemigrations

# Применить миграции
python3 manage.py migrate

# Запустить dev-сервер
python3 manage.py runserver

# Открыть Django shell
python3 manage.py shell

# Собрать статику (для продакшена)
python3 manage.py collectstatic

# Запустить тесты
python3 manage.py test
```

---

## Деплой

1. Установите `DEBUG = False` и заполните `ALLOWED_HOSTS` в `ITCSite/settings.py`.
2. Сгенерируйте новый `SECRET_KEY` (см. выше).
3. При необходимости перейдите на PostgreSQL.
4. Выполните `python3 manage.py collectstatic`.
5. Запустите через Gunicorn + Nginx:
   ```bash
   gunicorn ITCSite.wsgi:application --bind 0.0.0.0:8000
   ```
6. Настройте Nginx для раздачи `media/` и `static/`.

---

## Лицензия

Этот проект распространяется под лицензией [MIT](LICENSE).

---

## Вклад (Contributing)

Подробнее — в файле [CONTRIBUTING.md](CONTRIBUTING.md).
