# Access Control Systems

Существует несколько разных модель контроля доступа. В реальных системах обычно применяются комбинации из разных моделей.


# RBAC Level 1 — Flat RBAC

Самая простая модель.

```text
User → Role → Permission
```

Пример:

```text
User
 └ ROLE_ADMIN
     ├ USER_DELETE
     ├ USER_EDIT
     └ USER_VIEW
```

Проверка:

```php
$user->hasRole('ROLE_ADMIN')
```

---

## Проблемы

Если появляется 10 ресурсов и 10 действий:

```text
USER_CREATE
USER_DELETE
ACCOUNT_CREATE
ACCOUNT_DELETE
PROJECT_CREATE
PROJECT_DELETE
...
```

То роли превращаются в:

```text
ROLE_USER_ADMIN
ROLE_ACCOUNT_ADMIN
ROLE_PROJECT_ADMIN
```

И начинается **role explosion**.

---

# RBAC Level 2 — Hierarchical RBAC

Добавляется **иерархия ролей**.

```text
ROLE_ADMIN
   ↑
ROLE_MANAGER
   ↑
ROLE_USER
```

Admin автоматически получает всё:

```text
ROLE_ADMIN
 ├ ROLE_MANAGER
 └ ROLE_USER
```

Symfony умеет это:

```yaml
role_hierarchy:
    ROLE_ADMIN: ROLE_USER
    ROLE_SUPER_ADMIN: ROLE_ADMIN
```

---

# RBAC Level 3 — Constrained RBAC

Добавляются **ограничения и контекст**.

Например:

```text
user может быть ADMIN
но только в organization X
```

Появляется **membership**:

```text
User
Organization
Membership
    role
```

То есть:

```text
User → Membership → Role
```

Это уже **контекстная роль**.

---

# Почему Level 3 важен

Без него невозможно построить:

* Slack
* GitHub
* Notion
* Stripe
* Google Docs

Потому что пользователь может быть:

```text
Admin в Workspace A
Viewer в Workspace B
```

---

# 3. ABAC — Attribute Based Access Control

ABAC = **доступ на основе атрибутов**.

Attribute-based access control

Решение принимается на основе **атрибутов**.

Пример:

```text
user.department == resource.department
```

или

```text
user.id == document.owner_id
```

или

```text
document.status != archived
```

---

## Пример ABAC

```php
if ($document->ownerId === $user->id) {
    return true;
}
```

Это **ownership rule**.

---

Другой пример:

```php
if ($campaign->archived) {
    return false;
}
```

Это **resource attribute**.

---

# 4. ReBAC — Relationship Based Access Control

ReBAC = **доступ на основе отношений**.

Relationship-based access control

Решение принимается на основе **связи между объектами**.

---

## Пример ReBAC

GitHub:

```text
User
 └ member of
Organization
 └ owns
Repository
```

Проверка:

```text
User → Organization → Repository
```

---

Другой пример:

```text
User
 └ member
Team
 └ access
Project
```

Проверка:

```text
User → Team → Project
```

---

# 5. В реальности системы комбинируют всё



Они комбинируют:

```text
RBAC + ABAC + ReBAC
```

---

## Пример реальной проверки

```php
canEditAccount(user, account)
```

Проверка может быть:

```text
1 RBAC
   user имеет ROLE_SUPER_ADMIN

2 ReBAC
   user member of account

3 RBAC (membership role)
   membership.role == ADMIN

4 ABAC
   account.archived == false
```
