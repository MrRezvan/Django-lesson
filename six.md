# 🎯 **Шестой урок**

Сегодня мы **вспомним** / **научимся**, как рендерить **внутренние URL** в шаблонах Django и создавать **динамические ссылки**. 📌<br>
Разберем всё на примере страницы **отображения заказов** и **конкретного товара**.<br>

🚀 **Сначала сделаем один пример вместе, затем — самостоятельно.**

---
## 🔹 **📌 Можете посмотреть раздел в [шпаргалке (Динамические маршруты)](https://github.com/xlartas/it-compot-backend-methods/blob/main/django-base.md#%D0%B4%D0%B8%D0%BD%D0%B0%D0%BC%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B8%D0%B5-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D1%8B).**

---

## 🛒 **1. Создание URL для отображения конкретного товара**
📍 **Задача**: при запросе **GET** `http://127.0.0.1:8000/shop/product/1/`
мы хотим видеть информацию о товаре с `id = 1`. 💡<br>

### 🔹 **Определяем URL-маршрут в `urls.py`**:
```python
# shop/urls.py
urlpatterns = [
    ...
    path('shop/product/<int:product_id>/', views.product_detail, name='product_detail'),
]
```
🔹 `<int:product_id>` — динамическая часть URL, которая передается в представление `product_detail`. 🚀

---

## 🖥 **2. Создаём функцию `product_detail`**
```python
# shop/views.py
def product_detail(request, product_id):
    # Получаем товар по его ID
    product = Product.objects.get(id=product_id)
    # Передаём его в шаблон
    return render(request, 'shop/product_detail.html', {'product': product})
```
📌 Теперь при переходе по ссылке **`/shop/product/1/`**
в `product_id` будет передаваться **1**. 🔥

---

## 🖼 **3. Создаём шаблон `product_detail.html`**
📍 **Что добавляем?**
- **Название товара** в заголовок страницы.
- **Картинку** (с ограничением по ширине).
- **Оформить заказ** 🔘

```html
<!-- shop/product_detail.html  -->
{% extends 'shop/base.html' %}
{% load static %}
{% block title %}Shop | {{ product.name }}{% endblock %}

{% block content %}
    <div class="d-flex gap-3 flex-wrap justify-content-center mx-auto" style="max-width: 300px;">
        <div class="d-flex flex-column align-items-start text-center border-0 rounded-4 px-4 py-4"
             style="width: min-content; box-shadow: 0 0 5px #00000022;">
            <h1 class="text-wrap">{{ product.name }}</h1>
            <img width="340" src="{{ product.image.url }}" alt="">
            <span class="fs-2 fw-bold">{{ product.price }} ₽</span>
            <a class="btn fs-4 btn-outline-secondary w-100 mt-3">🛍 Оформить заказ</a>
        </div>
    </div>
{% endblock %}
```
✅ **Проверьте, что всё работает!**

---

## 🔗 **4. Рендер URL в шаблоне**
📌 **Теперь сделаем так, чтобы можно было кликнуть на товар в каталоге**.<br>
Используем **`{% url 'pattern_name' %}`**.

```html
<!-- shop/catalog.html -->
{% for product in products %}
    <a href="{% url 'product_detail' product_id=product.id %}"
       class="card border-0 rounded-4 text-decoration-none"
       style="width: 250px; box-shadow: 0 0 5px #00000022;">
        ...
    </a>
{% endfor %}
```
✅ Теперь **кликаем на товар — и переходим на его страницу!** 🎉

---

## 📦 **5. Добавляем ссылку на товар в заказах**
📌 **Сейчас в заказах отображается только имя товара.** Доработаем это!

👉 **Пусть ученики сами сделают так, чтобы при нажатии на товар из заказа он открывался!**

```html
{% for order in orders %}
    <div class="d-flex flex-column text-center border-0 rounded-4 px-4 py-2"
         style="width: min-content; box-shadow: 0 0 5px #00000022;">
        <span class="align-self-start fw-bold fs-5">Заказ №{{ order.id }}</span>
        
        <a href="{% url 'product_detail' product_id=order.product.id %}" class="text-decoration-none">
            {{ order.product.name }}
        </a>

        <span>{{ order.delivery_address|truncatewords:6 }}</span>
        <span>{{ order.created_at }}</span>
    </div>
{% endfor %}
```

---

## 🏗 **Дополнительное задание**
✅ **Если осталось время:**
- **Переделайте шапку** сайта — добавьте ссылки на **заказы** и **каталог**. 🔗

---

## 🏁 **Подведение итогов**
📌 **Что сделали?**
✅ Создали **динамические маршруты**.<br>
✅ Реализовали **переходы между страницами**.<br>
✅ Добавили **ссылки на товары в заказах**.<br>

🎉 **Отличная работа!** Теперь можно пушить код в репозиторий. 🚀
```sh
# git push...
```

