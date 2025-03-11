# Пятый урок

## По плану на урок:
- Добавление модели Order
- Регистрация админки для Order и создание заказа через админку
- Написание контроллера и вывод шаблона для orders

---

## 1. Создание модели Order

Начнём с добавления модели заказа (Order). Открываем файл `shop/models.py` и добавляем следующий код:

```python
from django.db import models
from shop.models import Product

class Order(models.Model):
    product = models.ForeignKey(Product, on_delete=models.SET_NULL, null=True)
    # ForeignKey создаёт связь с моделью Product
    # on_delete=models.SET_NULL означает, что если товар удалят, поле станет NULL
    
    delivery_address = models.CharField(max_length=255)
    # CharField — строковое поле для хранения адреса доставки
    
    created_at = models.DateTimeField(auto_now_add=True)
    # DateTimeField с auto_now_add=True автоматически устанавливает время создания заказа
```

После добавления модели выполняем миграции:

```sh
python manage.py makemigrations
python manage.py migrate
```

---

## 2. Регистрация модели Order в админке

Теперь зарегистрируем модель Order в административной панели. Открываем `shop/admin.py` и добавляем:

```python
from django.contrib import admin
from .models import Order

@admin.register(Order)
class OrderAdmin(admin.ModelAdmin):
    list_display = ('id', 'product', 'created_at')
    # list_display определяет, какие поля отображать в списке заказов в админке
```

После этого запускаем сервер:

```sh
python manage.py runserver
```

И переходим в браузере по адресу [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/), чтобы проверить, что модель Order добавилась в админку.

---

## 3. Контроллер для отображения заказов

Теперь добавим контроллер для отображения списка заказов в `shop/views.py`:

```python
from django.shortcuts import render
from .models import Order

def order_list(request):
    orders = Order.objects.all()
    return render(request, 'shop/orders.html', {'orders': orders})
```

Что здесь происходит:
1. Получаем все заказы из базы данных (`Order.objects.all()`).
2. Передаём их в шаблон `shop/orders.html` через контекст.
3. Шаблон будет использовать эти данные для отображения.

---

## 4. Настройка маршрутов

Открываем `shop/urls.py` и добавляем маршрут:

```python
from django.urls import path
from .views import order_list

urlpatterns = [
    path('orders/', order_list, name='order_list'),
]
```

Теперь страница заказов будет доступна по адресу [http://127.0.0.1:8000/orders/](http://127.0.0.1:8000/orders/).

---

## 5. Создание HTML-шаблона для списка заказов

Создадим файл `shop/templates/shop/orders.html` и добавим следующий код:

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Список заказов</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">История заказов</h2>
        <div class="row">
            {% for order in orders %}
            <div class="col-md-4">
                <div class="card mb-4">
                    <div class="card-body">
                        <h5 class="card-title">Заказ #{{ order.id }}</h5>
                        <p class="card-text"><strong>Товар:</strong> {{ order.product.name }}</p>
                        <p class="card-text"><strong>Адрес доставки:</strong> {{ order.delivery_address }}</p>
                        <p class="card-text"><strong>Дата заказа:</strong> {{ order.created_at }}</p>
                    </div>
                </div>
            </div>
            {% endfor %}
        </div>
    </div>
</body>
</html>
```

Что здесь происходит:
- Используем Bootstrap для стилизации.
- В цикле `{% for order in orders %}` отображаем карточки для каждого заказа.
- В карточке выводим ID заказа, название товара, адрес доставки и дату создания.

---

## 6. Возможные проблемы и их решения

**1. Ошибка: `No module named shop`**
- Убедитесь, что приложение `shop` добавлено в `INSTALLED_APPS` в `settings.py`.
- Запустите `python manage.py migrate`, если модель не была создана.

**2. Ошибка: `TemplateDoesNotExist`**
- Проверьте, что файл `orders.html` находится в `shop/templates/shop/`.
- В `views.py` путь к шаблону должен быть `'shop/orders.html'`, а не просто `'orders.html'`.

**3. Ошибка: `RelatedObjectDoesNotExist` при попытке вывести `order.product.name`**
- Это может случиться, если `product` равен `None`.
- Решение: использовать `{% if order.product %}{{ order.product.name }}{% else %}Товар удалён{% endif %}`.

---

На этом урок завершён! Теперь у нас есть:
✅ Модель заказов (Order) в базе данных
✅ Панель администратора для управления заказами
✅ Маршрут, контроллер и шаблон для вывода списка заказов 🚀
