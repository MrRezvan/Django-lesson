# 🔧 Второй урок: Создание модели заказов (Order)

## ✅ План урока:
- ➕ **Добавление модели `Order`**
- ⚙️ **Регистрация `Order` в админке и создание заказов**
- 📝 **Написание контроллера и шаблона для вывода заказов**

---

## ⚖️ 1. Добавление модели Order

### 📚 Что такое модель в Django?
Модель в Django — это класс, который описывает структуру таблицы базы данных. Мы используем модели для хранения и управления данными в проекте.

### 🔧 Создадим модель `Order`
Открываем файл `shop/models.py` и добавляем следующий код:

```python
class Order(models.Model):
    product = models.ForeignKey(Product, on_delete=models.SET_NULL, null=True)
    # ForeignKey связывает заказ с конкретным товаром
    # on_delete=models.SET_NULL позволяет сохранять заказ, даже если товар удалён
    
    delivery_address = models.CharField(max_length=255)
    # CharField для хранения адреса доставки, max_length=255 ограничивает длину строки
    
    created_at = models.DateTimeField(auto_now_add=True)
    # DateTimeField автоматически фиксирует дату и время создания заказа
```

### 🛠️ Разбираем код:
1. **`ForeignKey(Product, on_delete=models.SET_NULL, null=True)`** — связь с моделью `Product`. Если товар удалится, поле станет `NULL`, но заказ останется.
2. **`CharField(max_length=255)`** — текстовое поле для хранения адреса.
3. **`DateTimeField(auto_now_add=True)`** — автоматически записывает время создания заказа.

---

## 👨‍💼 2. Регистрация модели в админке

### 🔧 Добавляем `Order` в `admin.py`
Чтобы можно было управлять заказами через админку, добавим модель в `shop/admin.py`:

```python
from django.contrib import admin
from .models import Order

@admin.register(Order)
class OrderAdmin(admin.ModelAdmin):
    list_display = ('id', 'product')
    # Определяем, какие поля показывать в списке заказов
```

### ✅ Проверяем
1. Запускаем сервер: `python manage.py runserver`
2. Переходим в админку: [http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin)
3. Создаём новый заказ через панель администратора.

---

## 💻 3. Создаём контроллер и шаблон

### 🔧 Обновляем `views.py`
Добавляем в `shop/views.py` функцию для отображения списка заказов:

```python
from django.shortcuts import render
from .models import Order

def orders(request):
    orders = Order.objects.all()
    return render(request, 'shop/orders.html', {'orders': orders})
```

### 🛠️ Разбираем код:
1. **`Order.objects.all()`** — получаем все заказы из базы данных.
2. **Передаём их в шаблон** `orders.html`, чтобы вывести на страницу.

### 🌐 Настраиваем `urls.py`
Добавляем новый маршрут в `shop/urls.py`:

```python
from django.urls import path
from .views import order_list

urlpatterns = [
    path('orders/', orders, name='orders'),
]
```

Теперь страница с заказами будет доступна по адресу: [http://127.0.0.1:8000/orders](http://127.0.0.1:8000/orders)

---

## 🎨 4. Создаём шаблон `orders.html`

### 🔧 Код шаблона
Открываем `shop/templates/shop/orders.html` и добавляем:

```html
<h2 class="mb-4">Order History</h2>
<div class="row justify-content-center">
    {% for order in orders %}
    <div class="col-12 col-md-6 col-lg-4 mb-4 d-flex justify-content-center">
        <div class="card h-100 d-flex flex-column justify-content-between" style="max-width: 340px; box-shadow: 0 0 5px #00000022;">
            <div class="card-body text-center">
                <h5 class="card-title text-wrap">Order #{{ order.id }}</h5>
                <p class="card-text fs-6"><strong>Product:</strong> {{ order.product.name }}</p>
                <p class="card-text fs-6"><strong>Delivery Address:</strong> {{ order.delivery_address|truncatewords:6 }}</p>
                <p class="card-text fs-6"><strong>Order Date:</strong> {{ order.created_at }}</p>
            </div>
            <div class="card-footer bg-transparent border-0 d-flex justify-content-center">
                <a href="#" class="btn btn-primary w-100">View Details</a>
            </div>
        </div>
    </div>
    {% endfor %}
</div>
```

### 🛠️ Разбираем код:
- **`{% for order in orders %}`** — проходим по всем заказам.
- **Выводим номер заказа, товар, адрес и дату создания.**
- **Создаём Bootstrap-карточки** для удобного отображения.

---

## ⚠️ Возможные ошибки и их решение

| Ошибка | Причина | Решение |
|--------|--------|---------|
| `ForeignKey` ссылается на `Product`, но ошибка `RelatedObjectDoesNotExist` | В заказе нет продукта | Добавьте `null=True` в `ForeignKey` или убедитесь, что в БД есть продукты |
| `OperationalError: no such table: shop_order` | База данных не обновлена | Выполните `python manage.py makemigrations` и `python manage.py migrate` |
| `TemplateDoesNotExist: shop/orders.html` | Шаблон не найден | Убедитесь, что файл `orders.html` лежит в `shop/templates/shop/` |

---

## 🎯 Итог
Сегодня мы:
✅ Создали модель `Order`
✅ Зарегистрировали её в админке
✅ Вывели список заказов на страницу
✅ Настроили красивый шаблон

В следующем уроке разберём, как создать страницу оформления заказа! 🚀
