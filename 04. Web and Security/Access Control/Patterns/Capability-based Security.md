Capability-based security — это другой способ моделировать доступ. Он часто используется в больших API-системах (SaaS, internal platforms), потому что он **проще, быстрее и легче масштабируется**, чем классическая схема voters в Symfony.

Сначала разберём идею, потом посмотрим как она выглядит в коде.

---

# 1. Основная идея Capability-based security

В классической модели Symfony:

```
User
  ↓
ROLE
  ↓
VOTER
  ↓
PERMISSION
  ↓
SUBJECT
```

То есть каждый раз система **вычисляет** доступ.

Capability-based модель делает иначе:

```
User
  ↓
Capability
```

Capability — это **уже готовое право**.

Например:

```
org:1:view
org:1:edit
account:10:view
account:10:edit
account:11:view
```

То есть capability — это:

```
RESOURCE : ID : ACTION
```

---

# 2. Пример capabilities пользователя

Допустим пользователь:

* admin организации 1
* viewer account 10

Его capabilities могут быть:

```
org:1:view
org:1:edit
org:1:membership:view

account:10:view
```

И всё.

---

# 3. Проверка доступа

Теперь проверка доступа становится:

```
есть ли capability?
```

Например:

```
GET /v1/account/10
```

проверка:

```
account:10:view
```

---

# 4. Почему это очень быстро

Проверка — это просто:

```
hash lookup
```

например:

```
isset($capabilities['account:10:view'])
```

Это **O(1)**.

Нет:

* voters
* decision manager
* SQL
* сложных проверок

---

# 5. Где хранятся capabilities

Обычно:

```
JWT
session
in-memory cache
```

Пример:

```php
class SecurityToken
{
    private array $capabilities;
}
```

---

# 6. Пример capabilities

```php
[
    "org:1:view" => true,
    "org:1:edit" => true,
    "account:10:view" => true,
]
```

---

# 7. Проверка в коде

Можно сделать простой сервис:

```php
class CapabilityChecker
{
    public function can(string $capability): bool
    {
        return isset($this->capabilities[$capability]);
    }
}
```

Использование:

```php
$cap->can("account:{$accountId}:view");
```

---

# 8. Пример контроллера

```php
public function getAccount(Account $account)
{
    $this->cap->require(
        "account:{$account->getId()}:view"
    );

    ...
}
```

---

# 9. Как генерируются capabilities

При логине или token refresh.

Например:

```
user memberships
  ↓
organizations
  ↓
accounts
  ↓
roles
```

Из этого генерируется capability-набор.

Пример:

```
ROLE_ORGANIZATION_ADMIN (org 1)
```

генерирует:

```
org:1:view
org:1:edit
org:1:membership:view
account:*:view (в этой организации)
```

---

# 10. Wildcards

Часто используют wildcard:

```
org:1:*
account:*:view
```

или:

```
platform:*
```

Например:

```
ROLE_SUPER_ADMIN
```

даёт capability:

```
*
```

---

# 11. Как выглядит проверка с wildcard

Capability checker:

```
account:10:view
```

проверяет:

```
account:10:view
account:*:view
*
```

---

# 12. Пример структуры

```
Security
 ├ Capability
 │   ├ CapabilityChecker
 │   ├ CapabilityBuilder
 │   └ CapabilityMatcher
```

---

# 13. Пример CapabilityBuilder

```php
class CapabilityBuilder
{
    public function build(User $user): array
    {
        $caps = [];

        foreach ($user->memberships as $m) {

            if ($m->role === 'ROLE_ORGANIZATION_ADMIN') {

                $caps["org:{$m->orgId}:*"] = true;

            }
        }

        return $caps;
    }
}
```

---

# 14. Почему это любят большие API

Потому что это:

```
очень быстро
очень просто
очень предсказуемо
```

Нет:

```
AccessDecisionManager
voter priority
strategy
short circuit
```

---

# 15. Где используется

Такая модель используется (в разных вариациях):

* Stripe
* GitHub
* Google Zanzibar-style systems
* Slack-style SaaS

---

# 16. Когда capability-based модель хуже

Она плохо подходит если:

```
permissions очень динамические
```

Например:

```
доступ зависит от 20 условий
```

Тогда voters удобнее.

---

# 17. Гибридная модель (самая популярная)

Очень часто используют:

```
Capability
  +
Policy
```

Схема:

```
capability → coarse access
policy → fine rules
```

Например:

```
account:10:edit
```

есть, но policy проверяет:

```
account not archived
```

---

# 18. Итог

Классическая Symfony security:

```
ROLE
 → PERMISSION
 → SUBJECT
 → VOTER
```

Capability-based security:

```
CAPABILITY
 → RESOURCE:ID:ACTION
```

Проверка:

```
isset(capability)
```

---

💡 Если хочешь, могу показать ещё один **очень интересный момент**:

Как **Stripe-style SaaS** решают самую сложную проблему ACL —

```
user
  → organization
  → account
  → resource
```

когда у пользователя **тысячи объектов**, и capability-модель начинает взрываться по размеру.

Там используется техника **compressed permission graphs**, и это довольно интересная архитектура.
