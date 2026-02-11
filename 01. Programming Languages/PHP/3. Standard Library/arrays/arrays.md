array_map

array_walk

когда можно обращаться к несуществующим ключам? при добавлении можно? всякие конструкции и тд? Чтобы не падали нотисы и ошибки

Что тут с видимостью? Почему без use работает?
```php

        return array_map(function (string $code) {
            if ($this->isBackupCodesIssued()) {

            }

            return (new BackupCode())
                ->setCode($code)
                ->setUsed(false)
            ;
        }, $this->backupCodes);

```


```php
[,,,, $userId] = explode('_', $key);
```

```php
<?php
$data = [
    [
        "action" => "profile_add_account",
        "userID" => "76988",
        "accountID" => "0",
    ],
    [
        "action" => "profile_add_account",
        "userID" => "76988",
        "accountID" => "0",
    ]
];

// Получение массива userID
$userIDs = array_column($data, 'userID');

print_r($userIDs);

Array
(
    [0] => 76988
    [1] => 76988
)
?>
```