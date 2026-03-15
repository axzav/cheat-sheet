Конечно! Ниже приведена **очищенная версия запроса**, в которой оставлены только **ключевые и значимые поля** без внутренней сериализации и технических деталей потока (`_streams`, `_valueLength` и прочее служебное от `form-data` библиотеки):

---

### ✅ **Суть запроса:**

#### **Метод и URL:**

```
POST http://api-one-login.local/v1/providers
```

#### **Заголовки:**

```http
Authorization: Bearer <JWT_TOKEN>
Content-Type: multipart/form-data; boundary=--------------------------boundary
```

#### **Тело (multipart/form-data):**

```plaintext
----------------------------boundary
Content-Disposition: form-data; name="provider"

test
----------------------------boundary
Content-Disposition: form-data; name="domains[]"

test.com
----------------------------boundary
Content-Disposition: form-data; name="domains[]"

test.net
----------------------------boundary
Content-Disposition: form-data; name="metadata"; filename="text.xml"
Content-Type: application/xml

<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
----------------------------boundary--
```

---

### 📌 **Обобщённо — структура данных:**

```http
POST /v1/providers
Authorization: Bearer <токен>
Content-Type: multipart/form-data

provider = "test"
domains[] = "test.com"
domains[] = "test.net"
metadata (файл) = text.xml (тип: application/xml, содержимое: XML)
```

---

Если нужно, могу помочь с **реализацией этого запроса** в:

* `curl`
* `fetch` (JavaScript)
* `axios`
* `Node.js (form-data)`
* `Postman`

Просто скажи, в каком формате.
