# 📖 Третий урок

## ✅ По плану на урок:
- Настроить админку для создания товара
- Сделать красивый шаблон для вывода товаров в каталоге

Прошлый урок закончили на создании модели Товара. Теперь мы создадим объект товара через панель администратора.

---

## 👤 Шаг 1: Создаём суперпользователя

Суперпользователь — это администратор с полным доступом к панели управления Django. Он может управлять всеми данными, создавать и редактировать записи, а также управлять другими пользователями.

В терминале выполняем команду:

```bash
python manage.py createsuperuser
```

Заполняем поля `username` и `password` одинаковым значением для удобства, например – `admin`.

> ❗ **Важно:** при вводе пароля терминал не отображает символы, но пароль вводится!

---

## 🔒 Шаг 2: Регистрация модели в админке

Чтобы модель `Product` появилась в панели администратора, её нужно зарегистрировать.

1. Откройте файл `shop/admin.py`.
2. Добавьте код для регистрации модели:

```python
from django.contrib import admin
from .models import Product

@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    list_display = ('name', 'price', 'stock', 'is_available')
```

### ✅ Разбор кода:
- `@admin.register(Product)`: автоматически регистрирует модель `Product` в админке и связывает её с `ProductAdmin`.
- `list_display`: определяет, какие поля модели будут отображены в списке объектов в админке.

Теперь можно запускать сервер и зайти по адресу:

```
http://127.0.0.1:8000/admin/
```

Создаём свой первый товар! 🎉

---

## 📈 Отображение товаров в каталоге

Чтобы выводить товары в каталоге, передадим данные о товарах через контекст.

Открываем файл `views.py` и расширяем функцию `catalog`:

```python
def catalog(request):
    # Получаем все товары из базы данных
    products = Product.objects.all()
    
    # Передаём их в контекст
    return render(request, 'shop/product_list.html', {'products': products})
```

### ✅ Разбор кода:
1. `Product.objects.all()`:
   - Получает все записи из модели `Product`.
   - Это список всех товаров, хранящихся в базе данных.
2. **Контекст**:
   - Передаём словарь в `render`, где ключ `'products'` используется в шаблоне для доступа к списку товаров.
3. **Шаблон**:
   - Данные передаются в `shop/product_list.html`.

---

## 📝 Подключаем Bootstrap

**Bootstrap** — это популярный CSS-фреймворк для создания адаптивных и стильных веб-интерфейсов.

Переходим по ссылке и копируем CDN Bootstrap:

[Bootstrap 5.3 CDN](https://getbootstrap.com/docs/5.3/getting-started/introduction/#cdn-links)

Теперь выбираем готовый шаблон карточки товара и вставляем его в `catalog.html`:

```html
{% for product in products %}
<div class="card" style="width: 18rem;">
  <img src="{{ product.image.url }}" class="card-img-top" alt="...">
  <div class="card-body">
    <h5 class="card-title">{{ product.name }}</h5>
    <p class="card-text">{{ product.desc }}</p>
    <a href="#" class="btn btn-primary">Go somewhere</a>
  </div>
</div>
{% endfor %}
```

### ✅ Разбор кода:
- Используется цикл `{% for product in products %}` для динамического вывода товаров.
- Поля `product.name`, `product.desc`, `product.image.url` подставляются в разметку.

После этого дорабатываем внешний вид страницы в зависимости от оставшегося времени.

🔗 **На этом урок заканчивается!**

---

## 🔧 Возможные проблемы и их решения:

### 1. **Ошибка: `command not found: python` или `command not found: python3`**
**Решение:** Проверьте, установлен ли Python, и используйте `python3` вместо `python` в командах.

### 2. **Ошибка: `Superuser creation failed` при создании суперпользователя**
**Решение:** Проверьте, что база данных была создана (`python manage.py migrate`), и попробуйте снова.

### 3. **Ошибка: `does not exist in the database` при попытке зайти в админку**
**Решение:** Убедитесь, что выполнили `python manage.py createsuperuser` и запустили сервер.

### 4. **Ошибка: `ImageField requires Pillow`**
**Решение:** Установите библиотеку `Pillow`:  
```bash
pip install Pillow
```

### 5. **Ошибка: `Product matching query does not exist`**
**Решение:** Убедитесь, что в базе данных есть товары. Если их нет, создайте в админке.

### 6. **Картинки товаров не отображаются**
**Решение:** Проверьте настройки `MEDIA_URL` и `MEDIA_ROOT` в `settings.py` и добавьте `urlpatterns` для медиафайлов:
```python
from django.conf import settings
from django.conf.urls.static import static

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

Теперь ваш Django-магазин стал удобнее! 🚀

