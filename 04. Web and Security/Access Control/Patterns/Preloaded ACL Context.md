# Preloaded ACL Context

Проблема стандартной ACL

Наивная реализация voters делает запросы:

```
user
 → membership
 → role
 → account
 → organization
```

Например:

```
SELECT membership
WHERE user_id = ?
AND organization_id = ?
```

Если API делает 30 проверок:

```
30 SQL queries
```

Это плохо.

---

# 3. Как делают большие SaaS

Большие системы (Stripe, GitHub, Slack-style) используют **preloaded ACL context**.

Идея:

```
все права пользователя загружаются один раз
```

при аутентификации.

---

# 4. ACL Context Object

После логина создаётся объект:

```
UserAclContext
```

пример:

```php
class UserAclContext
{
    public array $organizationRoles;
    public array $accountRoles;
    public array $platformRoles;
}
```

Пример данных:

```
platformRoles:
    ROLE_SUPPORT

organizationRoles:
    org_1 → ROLE_ORGANIZATION_ADMIN
    org_2 → ROLE_VIEWER

accountRoles:
    account_10 → ROLE_MANAGER
    account_11 → ROLE_VIEWER
```

---

# 5. Где он хранится

Чаще всего:

```
JWT
session
security token
```

Например:

```php
class AuthToken extends AbstractToken
{
    private UserAclContext $acl;
}
```

---

# 6. Тогда voter не ходит в БД

Вместо:

```
SELECT membership
```

делается:

```php
$acl->organizationRoles[$orgId]
```

или

```php
$acl->accountRoles[$accountId]
```

Это **O(1)** операция.

---

# 7. Пример voter

```php
$acl = $token->getAclContext();

if (isset($acl->accountRoles[$account->getId()])) {

    $role = $acl->accountRoles[$account->getId()];

    if ($role === 'ROLE_ADMIN') {
        return true;
    }
}
```

Без SQL.

---

# 8. Когда обновлять ACL

ACL context пересоздаётся:

```
login
token refresh
membership change
```

Иногда через:

```
cache invalidation
```

---

# 9. Огромный плюс

Проверка доступа становится:

```
~50 ns
```

вместо

```
2–5 ms SQL
```

Для API это огромная разница.

---

# 10. Архитектура security

В больших проектах структура обычно такая:

```
Security
 ├ Acl
 │   ├ UserAclContext
 │   ├ AclLoader
 │   └ AclCache
 │
 ├ Policy
 │   ├ OrganizationPolicy
 │   ├ AccountPolicy
 │
 ├ Voter
 │   └ PolicyVoter
```

---

# 11. Поток запроса

```
request
 ↓
authenticator
 ↓
AclLoader
 ↓
UserAclContext
 ↓
Token
 ↓
Voter
 ↓
Policy
```

---

# 12. Пример Policy

```php
class AccountPolicy
{
    public function view(UserAclContext $acl, Account $account): bool
    {
        if ($acl->isSuperAdmin()) {
            return true;
        }

        return isset($acl->accountRoles[$account->getId()]);
    }
}
```

---

# 13. Почему это масштабируется

Даже если у пользователя:

```
500 accounts
```

всё хранится в:

```
memory map
```

и проверки мгновенные.

---

# 14. Это особенно важно для

```
multi-tenant SaaS
```

где:

```
user → organizations → accounts
```

---

# 15. Итоговая архитектура

```
ROLE
    глобальная позиция пользователя

PERMISSION
    действие

SUBJECT
    объект

CONTEXT
    всё остальное
```

А оптимизация:

```
ACL CONTEXT
    → загружается один раз
    → voters не ходят в БД
```

---

💡 Если хочешь, могу ещё показать **одну архитектуру, которая используется в очень больших Symfony API**:

она называется **Capability-based security** и позволяет полностью избавиться от voters и `is_granted()` — и сделать security быстрее и проще.
