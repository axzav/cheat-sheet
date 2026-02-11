Стоит ли в этом коде делать передачу users по ссылке? /** * @param int $accountId * @param array $users * @param int $page * @param int $maxPerPage * @return array */ public function getAllUsersByAccount(int $accountId, array $users = [], int $page = 1, int $maxPerPage = 100): array { $data = $this->userRepository->getUsers([$accountId], ['_page' => $page, '_max_per_page' => $maxPerPage]); $users = array_merge($users, $data->getValues()->toArray()); if ($data->getPages() === $page) { return $users; } else { return $this->getAllUsersByAccount($accountId, $users, $page + 1, $data->getMaxPerPage()); } }

Передача массива `users` по ссылке в данном контексте не обязательна, так как вы передаёте массив как аргумент функции, и его изменение внутри функции не будет влиять на исходный массив из вызывающей функции (поскольку массив передаётся по значению). Однако PHP использует механизм "copy-on-write" для массивов, что означает, что массив не будет скопирован, пока его не изменят. Это снижает накладные расходы.

Если массив очень большой, и вам важна оптимизация по памяти, можно рассмотреть передачу по ссылке. В этом случае изменения будут производиться непосредственно на исходном массиве, что может снизить нагрузку на память. Это делается с помощью символа `&`, например:

```php
public function getAllUsersByAccount(int $accountId, array &$users = [], int $page = 1, int $maxPerPage = 100): array
{
    $data = $this->userRepository->getUsers([$accountId], ['_page' => $page, '_max_per_page' => $maxPerPage]);

    $users = array_merge($users, $data->getValues()->toArray());

    if ($data->getPages() === $page) {
        return $users;
    } else {
        return $this->getAllUsersByAccount($accountId, $users, $page + 1, $data->getMaxPerPage());
    }
}
```

Однако стоит учитывать, что передача по ссылке может увеличить сложность понимания кода и сопровождения. Решение стоит принимать исходя из конкретных требований к производительности и объёму данных.

> Однако PHP использует механизм "copy-on-write" для массивов, что означает, что массив не будет скопирован, пока его не изменят.

Это значит что при array_merge($users, $data->getValues()->toArray()); будет создан новый массив?

Да, при вызове `array_merge($users, $data->getValues()->toArray())` будет создан новый массив.

Механизм "copy-on-write" в PHP означает, что массив не будет скопирован, пока к нему не применяют операции, которые изменяют его. Но так как `array_merge` создаёт новый массив, результатом этой операции будет создание нового массива, и память, занятую оригинальным массивом `$users`, будет скопирована в новый массив, а затем добавлены элементы из второго массива.

Таким образом, несмотря на оптимизацию с "copy-on-write", в этом случае PHP будет вынужден создать новую копию массива для хранения результата.
