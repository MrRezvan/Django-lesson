# 🏁 Первый урок: Начало работы с Django

## 📌 По плану на урок:
✅ Создать виртуальное окружение и запустить его  
✅ Создать корневую папку проекта  
✅ Добавить папку с приложением  
✅ Настроить папку templates  
✅ Создать файлы HTML  
✅ Запустить локальный сервер  

---

## 📂 1. Подготовка проекта

### 📌 Создание папки проекта
Создаём папку на рабочем столе и открываем её в **VS Code**.  
*Чтобы открыть терминал в VS Code:*  
▶ **View → Terminal** или сочетание клавиш `Ctrl + ` `

---

## ⚙️ 2. Установка и настройка

### 🔹 Создание виртуальной среды с именем `venv_name`
```sh
python -m venv venv_name
```

### 🔹 Активация виртуального окружения
🔹 **Windows (cmd):**
```sh
venv_name\Scripts\activate.bat
```
🔹 **Windows (PowerShell):**
```sh
venv_name\Scripts\activate
```
🔹 **Mac/Linux:**
```sh
source venv_name/bin/activate
```

📌 *Виртуальное окружение необходимо для изоляции зависимостей проекта.*

### 🔹 Установка Django
```sh
pip install Django
```

### 🔹 Создание Django-проекта
```sh
django-admin startproject project_name
```
📌 *Папку проекта можно назвать `config`, чтобы подчеркнуть, что это конфигурация.*

### 🔹 Переход в директорию проекта
```sh
cd project_name
```

### 🔹 Запуск сервера Django
```sh
python manage.py runserver
```
📌 *Сервер принимает HTTP-запросы и передаёт их в Django.*

---

## 🏗 3. Создание приложения

### 🔹 Создаём Django-приложение
```sh
python manage.py startapp app_name
```

### 🔹 Добавляем приложение в `settings.py`
```python
INSTALLED_APPS = [
    ..., 
    'app_name',
]
```

📌 *Добавление в `INSTALLED_APPS` позволяет Django загружать модели и маршруты приложения.*

---

## 📂 4. Настройка шаблонов

### 🔹 Создаём папку `templates/shop`
📌 Внутри неё создаём файлы:
- `orders.html`
- `catalog.html`
- `order_create.html`

### 🔹 Добавляем представление в `views.py`
```python
def catalog(request):
    return render(request, 'shop/catalog.html')
```
📌 *Представление обрабатывает запрос и возвращает HTML-шаблон.*

---

## 🔗 5. Настройка маршрутов

### 🔹 Создаём `urls.py` в `shop`
```python
from django.urls import path
from shop.views import catalog

urlpatterns = [
    path('', catalog),
]
```

### 🔹 Подключаем маршруты в `config/urls.py`
```python
from django.urls import path, include

urlpatterns = [
    path('', include('shop.urls')),
]
```

📌 *Теперь `catalog.html` будет загружаться при открытии главной страницы.*

---

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
