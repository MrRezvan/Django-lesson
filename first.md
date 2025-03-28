# 🏫 Первый урок

## 📌 По плану на урок:

- ✅ Создать виртуальное окружение и запустить его
- 📁 Создать корневую папку проекта
- 📂 Добавить папку с приложением
- 🎨 Настроить папку `templates`
- 📝 Создать файлы `html`
- 🚀 Запустить локальный сервер

---

## 📂 Начало работы

🔹 **Всё начинается с создания папки на рабочем столе и запуска этой папки в VS Code.**

Затем начинаем прописывать команды в терминале.

💡 *Чтобы открыть терминал, нажимаем сверху **View → Terminal** или сочетание клавиш `Ctrl + \``.*

---

## 🌍 Создание виртуального окружения

```sh
python -m venv venv_name
```

### ▶ Активация виртуального окружения
```sh
./venv_name/Scripts/activate
```
📌 Виртуальное окружение (`virtual environment`) в Python создаётся для изоляции зависимостей проекта от глобальных библиотек Python. Это помогает избежать конфликтов между версиями библиотек для разных проектов.

---

## 🛠 Установка Django
```sh
pip install Django
```

---

## 📦 Создание Django-проекта
```sh
django-admin startproject project_name
```
📍 Это стартовая точка для любого Django-проекта, где будут задаваться общие настройки, используемые приложениями внутри проекта.

💡 *Название "config" часто выбирают, чтобы подчеркнуть, что это конфигурационная часть проекта. Но название можно выбрать любое.*

### 📂 Переход в директорию проекта
```sh
cd ./project_name
```

### ▶ Запуск сервера Django
```sh
python manage.py runserver
```
📌 Сервер принимает HTTP-запросы от браузера, передаёт их в Django для обработки и возвращает готовый ответ (например, HTML-страницу).

---

## 🏗 Создание Django-приложения
```sh
python manage.py startapp app_name
```
📌 Это создаст структуру нового приложения в проекте с файлами для:
- обработки данных (**models.py**),
- отображения страниц (**views.py**),
- маршрутов (**urls.py**),
- шаблонов (**templates/**).

### 🔗 Добавление приложения в проект
В `settings.py` добавляем строку:
```python
INSTALLED_APPS = [
    ..., 
    ..., 
    'app_name',
]
```
📌 Это нужно, чтобы Django учитывал приложение, загружал его модели, маршруты и шаблоны.

---

## 📁 Настройка шаблонов
Создадим папку **templates** в `shop/` и внутри неё — папку `shop/`.

📌 Это позволит хранить HTML-шаблоны, а дополнительная папка `shop` поможет не запутаться, относя шаблоны к конкретному приложению.

Создаём три HTML-файла:
- `orders.html`
- `catalog.html`
- `order_create.html`

---

## 📄 Создание представления (View)
Добавляем функцию `catalog` в `views.py`:
```python
def catalog(request):
    return render(request, 'shop/catalog.html')
```
📌 **Что это?** Представление (`view`), которое обрабатывает запросы и возвращает HTML-страницу.

📌 **Что делает?**
1. Принимает объект `request`, содержащий информацию о запросе пользователя.
2. Вызывает функцию `render()`, чтобы вернуть HTML-шаблон `shop/catalog.html`.

📌 **Зачем?** Это создаёт логику обработки страниц: какие данные подставлять и что показывать.

---

## 🔗 Настройка маршрутов (URLs)
Создаём `urls.py` в `shop/`:
```python
from django.urls import path
from shop.views import catalog

urlpatterns = [
    path('', catalog),
]
```

### 📌 Подключение маршрутов приложения к проекту
Редактируем `config/urls.py`:
```python
from django.urls import path, include

urlpatterns = [
    path('', include('shop.urls')),
]
```
📌 Теперь при запуске проекта страницы будут доступны по заданным маршрутам.

---

## 🎬 Итоги урока

✅ Создали виртуальное окружение и настроили его.
✅ Установили Django и создали новый проект.
✅ Добавили приложение и настроили шаблоны.
✅ Написали первое представление и подключили маршруты.
✅ Запустили локальный сервер.

🚀 *На следующем уроке продолжим работу над проектом!*

## 🚨 Возможные проблемы и их решения

### ❌ Ошибка при активации виртуального окружения
**Проблема:** `command not found` при активации окружения.  
✅ **Решение:** Используйте правильную команду (см. раздел «Активация виртуального окружения»).  

---

### ❌ Django не установлен
**Проблема:** `django-admin: command not found`  
✅ **Решение:** Установите Django:  
```sh
pip install Django
```

---

### ❌ Ошибка при запуске сервера
**Проблема:** `ModuleNotFoundError: No module named 'django'`  
✅ **Решение:** Проверьте, что активировали виртуальное окружение.  
```sh
source venv_name/bin/activate  # Mac/Linux
venv_name\Scripts\activate    # Windows
```

---

### ❌ Не загружается HTML-шаблон
**Проблема:** Сервер работает, но шаблон не отображается.  
✅ **Решение:** Проверьте настройки в `settings.py`:
```python
TEMPLATES = [
    {
        ..., 
        'DIRS': [BASE_DIR / 'templates'],
        ...
    },
]
```
✅ Проверьте правильность пути в `render()`:
```python
return render(request, 'shop/catalog.html')
```

---

🎉 **Теперь первый урок завершён!** Django-проект создан, сервер запущен, а шаблоны подключены. 🚀
