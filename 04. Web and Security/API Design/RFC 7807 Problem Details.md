# RFC 7807 (Problem Details for HTTP APIs)

Стандарт ответа в случае ошибки.

Основной `Content-Type` для таких ответов: `application/problem+json`.

### Структура объекта:
* **`type` (URI):** Ссылка на страницу с описанием этой конкретной проблемы. Клиент может использовать её как уникальный код ошибки.
* **`title` (String):** Короткое, понятное человеку описание типа проблемы (не меняется от запроса к запросу).
* **`status` (Number):** Тот же HTTP статус-код (для удобства парсинга в теле).
* **`detail` (String):** Детали ошибки (например, Email 'test@test.com' уже занят).
* **`instance` (URI):** Ссылка на конкретный запрос, где возникла проблема (обычно путь эндпоинта).

Можно расширять этот объект. Например, для ошибок валидации можно добавить `invalid-params`:

```json
{
  "type": "https://example.com/probs/out-of-stock",
  "title": "Item is out of stock",
  "status": 400,
  "detail": "We only have 2 items left, but you requested 5.",
  "instance": "/orders/123/items",
  "invalid-params": [
    { "name": "quantity", "reason": "Must be less than or equal to 2" }
  ]
}
```

# Links:
- https://datatracker.ietf.org/doc/html/rfc7807
