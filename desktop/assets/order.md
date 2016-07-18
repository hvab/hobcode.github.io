# Заказ автомобиля

На этой странице аякс используется в двух местах.

## 1. Выбор модели
При выборе модели (например, Hyundai Solaris, где Hyundai - бренд, Solaris - модель, согласно админке) в селекте `id="orderFormModel"` срабатывает аякс-запрос. Модели в селект заполняются согласно админке. То есть, в строке `<option value="6">Hyundai Solaris</option>`, `Hyundai Solaris` - это полное название модели, а `6` - её уникальный идентификатор в БД. Скрипт берет значение `value=6` тега `<option>` и передает его на сервер в виде `/ajax-car-sets/?6`.

(Пока писал этот мануал, подумал, что тут тонкое место. Ты сможешь обработать данные или их надо передавать в виде `/ajax-car-sets/?id=6`?)

В ответ скрипт ждет данные в формате JSON в виде как указано для примера в файле `ajax-car-sets.json`. В файле содержится список всех автомобилей для этой модели.

### Структура файла `ajax-car-sets.json`
Так как это список объектов, то все данные заключаются в квадратные скобки, то есть, это массив.

**Пример**
```js
[
{
  "id": 1, // id автомобиля
  "title": "Hyunday Solaris, 2013, серый 1", // полное название авто
  "color": "http://www.bericar.django.top-p.ru/media/vehicle/81.jpg", // ссылка на файл с цветом авто
  "features": ["bags_1", "kp_m", "seats_2"], // фичи для этого авто нужно перечислять классами из css
  "images": [ // массив ссылок фоток для слайдера
  "http://www.bericar.django.top-p.ru/media/vehicle/hyundai-solaris-81.jpg",
  "http://www.bericar.django.top-p.ru/media/vehicle/hyundai-solaris-92.jpg",
  "http://www.bericar.django.top-p.ru/media/vehicle/hyundai-solaris-93.jpg"
  ]
},
{ // пример минимальных обязательных данных по авто, иначе ошибки полезут
  "id": 6,
  "title": "Hyunday Solaris, 2013, серый 4",
  "color": "http://www.bericar.django.top-p.ru/media/vehicle/81.jpg"
},
...

]
```

## 2. Выбор цвета автомобиля
При выборе цвета автомобиля, то есть, по сути, конкретного автомобиля, выполняется аякс-запрос для подсчета стоимости и скидки. Работает точно по тому же принципу, что и в мобильной версии.

Основываясь на данных, полученных при первом аякс-запросе (скрипт уже знает все id для данной модели, плюс собирает данные из формы), когда мы выбирали модель автомобиля в селекте, на сервер передаются данные в следующем виде
 `/ajax-get-price/?id=1&start=2016-06-15&end=2016-06-16`.
 Где `id` - это уникальный идентификатор автомодиля выбранного цвета, и даты.

 В ответ ждем JSON-данные как в файле `ajax-get-price.json`, который по структуре как в мобильной версии. Добавилось только поле для залога.

### Структура файла `ajax-get-price.json`
```js
{
 "days":132, // количество дней аренды
 "priceAll":9000, // общая цена с учетом скидки
 "discount":"1%", // размер скидки
 "pledge":3000 // залог
}
```

___

Если коротко, то данные для аякс-запросов берутся из html.
```html
<select name="orderFormModel" id="orderFormModel" class="form-control">
  <option value="6">Hyundai Solaris</option>
  <option value="7">Hyundai Solaris Hatch</option>
  <option value="12">Nissan Almera</option>
</select>

<input class="form-control datepicker" type="text" name="orderFormDateStart" id="orderFormDateStart" placeholder="Дата" required>

<input class="form-control datepicker" type="text" name="orderFormDateEnd" id="orderFormDateEnd" placeholder="Дата" required>
```
Остальное из JSON.
