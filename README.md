# Документация для sendit public api

URL для всех api-методов: http://sendit.ru/api/v1

## Аутентификация

Для доступа к API с каждым запросом нужно передавать http-токен (для получения токена, напишите на <a href="/mailto=company@sendit.ru?subject='Запрос токена API'">company@sendit.ru</a>)

Пример:
```
curl -H "Authorization: Token token=1bd3e675293b4365ydf175753f79994b" "http://sendit.ru/api/..."
```

## Ошибки

Если ошибка во входящих параметрах, то сервер вернет 400 статус и строку, в которой будут указанны все ошибки (отсутствие обязательного параметра, несуществующая страна и пр.)  
Если ошибка на стороне сервера, то сервер вернет 500 статус

## Методы

### GET /quotes

Запрос цен и сроков доставки

Пример:
```
curl -H "Authorization: Token token=1bd3e675293b4365ydf175753f79994b" "http://sendit.ru/api/v1/quotes?from%5Bcountry%5D=%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D1%8F&from%5Blabel%5D=%D0%9C%D0%BE%D1%81%D0%BA%D0%B2%D0%B0&to%5Bcountry%5D=%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D1%8F&to%5Blabel%5D=%D0%9F%D0%B5%D1%80%D0%BC%D1%8C&weight=4.5"
```
Пример с указанием провайдеров:
```
curl -H "Authorization: Token token=1bd3e675293b4365ydf175753f79994b" "http://sendit.ru/api/v1/quotes?from%5Bcountry%5D=%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D1%8F&from%5Blabel%5D=%D0%9C%D0%BE%D1%81%D0%BA%D0%B2%D0%B0&providers%5B%5D=Cse&providers%5B%5D=Spsr&to%5Bcountry%5D=%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D1%8F&to%5Blabel%5D=%D0%9F%D0%B5%D1%80%D0%BC%D1%8C&weight=4.5"
```
Cтрана по умолчанию - Россия, полный список можно посмотреть в выпадающем списке на сайте http://sendit.ru

Пример входящих параметров:

```
{
  from: { label: 'Название населенного пункта', country: 'Название страны' },
  to: { label: 'Название населенного пункта', country: 'Название страны' },
  weight: 4.5,
  providers: ['Ems', 'Spsr', 'Cse']
}
```

- **from** (обязательно) - от куда доставлять, именованный массив (хэш)  
- **to** (обязательно) - куда доставлять, именованный массив (хэш)  
- **weight** (обязательно) - вес (кг.), положительное число  
- **providers** - список провайдеров доставки, массив строк  
Если параметр не указан, производится поиск по всем доступным провайдерам (может занимать до 30 сек.) 
Список доступных провайдеров: Cse, Kts, UnionPost, Latella, Tnt, Pek, Fox, Ups, Dpd, Dellin, ExpressRu, Bringo, Dhl, Spsr, CityExpress, Cdek, Pony, Peshkariki

Пример возвращаемого результата:

```
{
  "Cse":[
    {
      "price":1980.0,
      "days":1,
      "type_name":"Сверхсрочная",
      "order_url":"http://sendit.ru/orders/new?quote_id=843555"
    },
    {
      "price":1320.0,
      "days":3,
      "type_name":"Срочная",
      "order_url":"http://sendit.ru/orders/new?quote_id=843556"
    },
    {
      "price":2688.0,
      "days":6,
      "type_name":"Эконом доставка",
      "order_url":"http://sendit.ru/orders/new?quote_id=843557"
    }
  ],
  "Latella":[
    {
      "price":736.0,
      "days":4,
      "type_name":"Стандарт",
      "order_url":"http://sendit.ru/orders/new?quote_id=843558"
    },
    ...
  ],
  ...
}
```

Формат:

```
{
  "НазваниеПровайдера":[
    {
      "price": цена,
      "days": кол-во дней доставки,
      "type_name": название типа доставки,
      "order_url": url на форму заказа данной доставки
    },
    ...
  ],
  ...
}
```
