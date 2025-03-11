# 🛍 **Оформление заказа**

Сегодня мы реализуем **оформление заказа** со страницы подробного просмотра товара.

---

## 🔗 **1. Переопределим маршрут `order_create`**

Сделаем маршрут **динамическим**, чтобы он принимал параметр `id` продукта. Это значит, что часть URL становится переменной. Например, `path('order_create/<int:product_id>/')` позволяет извлечь значение `product_id` из URL и передать его в соответствующее представление.

📌 Это удобно, когда нам нужно обработать запросы для разных объектов (в данном случае продуктов) без создания множества статических маршрутов. Таким образом, при переходе на URL `http://127.0.0.1:8000/shop/order_create/1/` параметр `1` будет передан в функцию `order_create` как значение переменной `product_id`.

```python
# shop/urls.py
urlpatterns = [
    path('order_create/<int:product_id>/', order_create, name='order_create'),
]
```

---

## 🎯 **2. Доделаем кнопку с оформлением заказа в `product_detail.html`**

Добавим `href`, который будет вести на оформление заказа для конкретного продукта по его `id`. Это достигается с помощью встроенного тега шаблона `{% url %}`, который генерирует правильный URL на основе имени маршрута и переданных параметров.

📌 При рендеринге шаблона строка `{% url 'order_create' product_id=product.id %}` будет преобразована в ссылку вида `http://127.0.0.1:8000/shop/order_create/1/`, где `1` — это `id` текущего продукта.

На странице `product_detail.html` доступна переменная `product`, которая содержит как раз нужный нам `id`.

```html
<!-- product_detail.html -->
...
<a href="{% url 'order_create' product_id=product.id %}"
   class="btn fs-4 btn-outline-secondary w-100 mt-3">Оформить заказ</a>
...
```

✅ **Проверьте, что всё работает**, и кнопка действительно перенаправляет нас на `order_create` с верно указанным `id` в адресной строке.

Пример: `http://127.0.0.1:8000/shop/order_create/1/`

---

## 📝 **3. Доделаем страницу для создания заказа**

### 🔍 **Предпросмотр товара**
Так как при оформлении заказа нам нужен **предпросмотр** того, что мы заказываем, скопируем код из `product_detail.html`, изменив заголовок `<h1>` и тег `<title>`.

### 📄 **Форма заказа**
Создадим форму для отправки данных для создания объекта `Order`.

📌 **Форма** — это средство взаимодействия пользователя с сервером, позволяющее отправить данные для обработки. Поля формы должны соответствовать полям модели, которые мы хотим заполнить.

📋 **Какие поля нам нужны?**
- Поле **product** — в форме **не нужно**, так как мы передаём `id` продукта через URL.
- Поле **delivery_address** — заполняется пользователем и отправляется на сервер через `POST` запрос.
- Поле **created_at** — заполняется автоматически, поэтому **не включается** в форму.

📌 Когда пользователь нажимает кнопку **отправки**, браузер формирует запрос, передаёт данные формы на сервер (в данном случае через `POST`) и ожидает ответ. Сервер обрабатывает запрос, проверяет данные и создаёт объект `Order` в базе данных.

### 📌 **HTML код формы**
```html
<!-- order_create.html -->
{% extends 'shop/base.html' %}
{% block title %}Shop | Order {{ product.name }} {% endblock %}

{% block content %}
    <div class="d-flex gap-3 flex-wrap justify-content-center mx-auto"
         style="max-width: 300px;">
        <div class="d-flex flex-column align-items-start text-center border-0 rounded-4 text-nowrap px-4 py-4"
             style="width: min-content; box-shadow: 0 0 5px #00000022;">
            <h1 class="text-wrap">Заказ {{ product.name }}</h1>
            <span class="fs-5">{{ product.desc }}</span>
            <form action="{% url 'order_create' product_id=product.id %}"
                  class="d-flex flex-column justify-content-center mx-auto mt-3"
                  method="post">{% csrf_token %}
                <input name="delivery_address" type="text"
                       class="form-control"
                       required
                       placeholder="Адрес доставки">
                <button type="submit"
                        class="btn fs-4 btn-outline-secondary w-100 mt-3">
                    Заказать</button>
            </form>
        </div>
    </div>
{% endblock %}
```

📌 **Ошибка:** Если попробовать нажать на кнопку "Оформить заказ" на странице детального просмотра, то возникнет ошибка. **Почему?** 🤔

### 🎯 **Передаём переменную `product` в шаблон**
Передадим переменную, как мы делали в `product_detail`.

```python
# shop/views.py
def order_create(request, product_id):
    product = Product.objects.get(id=product_id)
    return render(request, 'shop/order_create.html', {
        'product': product
    })
```

---

## 📩 **4. Принимаем и сохраняем данные**

Сейчас при отправке формы страница просто **перезагружается**. Почему? 🤔

📌 Мы обрабатываем запрос типа **GET**, хотя форма отправляет **POST**. Нам нужно добавить обработку данных `POST` запроса.

```python
# shop/views.py
def order_create(request, product_id):
    product = Product.objects.get(id=product_id)
    if request.method == 'POST':
        Order.objects.create(
            product=product,
            delivery_address=request.POST.get('delivery_address')
        )
    return render(request, 'shop/order_create.html', {
        'product': product
    })
```

---

## 🔄 **5. Делаем редирект с аргументами**

📌 **Вспоминаем, как делаются редиректы:**

```python
# shop/views.py
def order_create(request, product_id):
    product = Product.objects.get(id=product_id)
    if request.method == 'POST':
        Order.objects.create(
            product=product,
            delivery_address=request.POST.get('delivery_address')
        )
        return redirect('orders')
    return render(request, 'shop/order_create.html', {
        'product': product
    })
```

✅ **Теперь при успешном оформлении заказа пользователь будет перенаправляться на страницу заказов!** 🎉

## Возможные ошибки и их решения

| Ошибка | Причина | Решение |
|--------|---------|---------|
| **Product matching query does not exist.** | Продукт с таким `id` отсутствует в базе данных. | Убедитесь, что переданный `product_id` существует, либо добавьте обработку исключения `Product.DoesNotExist`. |
| **NoReverseMatch at /shop/product/...** | В шаблоне используется `{% url 'product_detail' product.id %}`, но маршрут `product_detail` не определён в `urls.py`. | Проверьте, что в `shop/urls.py` есть `path('shop/product/<int:product_id>/', views.product_detail, name='product_detail')`. |
| **TypeError: int() argument must be a string, a bytes-like object or a number, not 'NoneType'** | В `order_create` передаётся `None` вместо `product_id`. | Проверьте, что `product_id` передаётся в URL корректно. |
| **CSRF verification failed. Request aborted.** | В форме отсутствует `{% csrf_token %}`. | Добавьте `{% csrf_token %}` внутри формы перед `input`. |
| **Method Not Allowed (GET, HEAD, OPTIONS)** | Форма отправляет данные через `POST`, но представление не обрабатывает `POST` запросы. | Убедитесь, что `order_create` проверяет `request.method == 'POST'` перед обработкой формы. |
| **Cannot assign "<Product: Товар>": "Order.product" must be a "Product" instance.** | Ошибка при создании заказа: передаётся не объект `Product`, а его `id`. | В `Order.objects.create()` передавайте сам объект `Product`, а не `product_id`. |

