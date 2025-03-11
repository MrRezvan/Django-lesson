# 📌 Второй урок

## 📅 План на урок
✅ Добавление модели `Product`
✅ Создание папки `media` и настройка `media` путей

---

## 📦 Создание модели `Product`
В файле `shop/models.py` создаём модель:

```python
class Product(models.Model):
    name = models.CharField(max_length=100)  # Поле для хранения строк
    image = models.ImageField(upload_to='images/Product/')  # Поле для изображений
    desc = models.TextField()  # Текстовое поле без ограничения длины
    price = models.FloatField()  # Число с плавающей точкой
    discount = models.PositiveIntegerField(default=0)  # Положительное целое число
    rating = models.PositiveIntegerField()  # Оценка товара
    stock = models.PositiveIntegerField()  # Количество на складе
    is_available = models.BooleanField(default=True)  # Доступность товара
```

> **🔹 Зачем это нужно?**
> Эта модель создаёт структуру данных для товаров в магазине. Каждое поле определяет атрибут товара.

⚠️ **Важно!** Для работы с `ImageField` необходимо:
- Настроить медиафайлы (см. далее)
- Установить библиотеку `Pillow`:
  ```sh
  pip install Pillow
  ```

---

## 📂 Настройка папки `media`
### 1️⃣ Создаём папку `media`
В корневой директории проекта (там же, где `manage.py`), создаём папку `media`. Django будет сохранять туда загружаемые файлы.

### 2️⃣ Настраиваем `settings.py`
Добавляем настройки для медиафайлов:

```python
import os

MEDIA_URL = '/media/'  # URL для доступа к файлам
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')  # Путь к папке media
```

### 3️⃣ Настраиваем `urls.py`
Чтобы Django мог отдавать медиафайлы в режиме разработки:

```python
from django.conf import settings
from django.conf.urls.static import static

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

> **💡 Почему важно?**
> - В `DEBUG=True` файлы обслуживаются Django.
> - В `DEBUG=False` файлы должны обрабатываться сервером (например, Nginx, Apache).

---

## 🔄 Миграции базы данных

После создания модели необходимо выполнить миграции:

```sh
python manage.py makemigrations
python manage.py migrate
```

> **ℹ️ Что делают миграции?**
> - `makemigrations` — создаёт файлы миграций (изменения моделей)
> - `migrate` — применяет миграции к базе данных

🔹 Для удобного просмотра базы данных в `SQLite` можно установить плагин **SQLite Viewer** в VS Code.

---

## ❗ Возможные проблемы и их решения

| Проблема | Решение |
|----------|---------|
| 🔴 `ModuleNotFoundError: No module named 'PIL'` | Установите `Pillow`: `pip install Pillow` |
| 🔴 `ImageField требует наличия Pillow` | Убедитесь, что `Pillow` установлен (`pip freeze | grep Pillow`) |
| 🔴 Медиафайлы не отображаются в браузере | Проверьте настройки `MEDIA_URL` и `MEDIA_ROOT`, убедитесь, что в `urls.py` подключены `static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)` |
| 🔴 Ошибка при миграции `table already exists` | Удалите старую базу (`db.sqlite3`), затем `migrate --fake-initial` |

🎉 **На этом второй урок завершён!** 🚀
