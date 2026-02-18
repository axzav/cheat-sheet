# LogQL: Log Query Language

Запрос состоит из двух элементов - **log stream selector** и **filter expression**

## Log Stream Selector

**Log Stream Selector** содержит **log labels**, которые указывают **log stream** для запроса

```shell
{app="mysql",name="mysql-backup"}
```

**Label matching operator** может быть:
- `=`: exactly equal.
- `!=`: not equal.
- `=~`: regex matches.
- `!~`: regex does not match.

```shell
{name=~"mysql.+"}
{name!~"mysql.+"}
```


## Filter Expression

**Filter Expression** позволяют отфильтровать логи по разным выражениям:
```shell
{job="mysql"} |= "error"
{name="kafka"} |~ "tsdb-ops.*io:2003"
{instance=~"kafka-[23]",name="kafka"} != kafka.server:type=ReplicaManager
```

Поддерживаются следующие операторы:
`|=`: Log line contains string.
`!=`: Log line does not contain string.
`|~`: Log line matches regular expression.
`!~`: Log line does not match regular expression.

Выражения можно объединять в пайп:
```shell
{env="staging", service="api-php"} != "Event sent" != "API Request Audit"
```

## Parsers

**Parsers** позволяют распарсить содержимое лога на основе формата и отфильтровать по распарсеным полям
Например для JSON:
```shell
{"level":"error","msg":"timeout","code":504}
```
Запрос будет:
```shell
{job="api"} |= "error" | json | level="error" | code=504 | msg!="configuration"
```

Поддерживаются следующие парсеры:
- `json`
- `logfmt` (например `level=error msg="timeout" user=123`)
- `regexp` (`| regexp "(?P<status>[0-9]{3})" | status="500"`)
- `pattern` (`| pattern "<ip> - - <_> \"<method> <path> <_>\" <status> <_>" | status="500"`)

После парсинга появляются служебные поля:
`__error__` - ошибка парсинга
`__line__`
`__timestamp__`

Ошибку парсинга можно отфильтровать `__error__ = ""`, тогда в вывод попадут только успешно распарсеные логи
```shell
{job="containerlogs"} |~ "(?i)(service unavailable|maintenance|capacity|try again later|time(d |-)?out)" | json | __error__ = "" | msg!="configuration"
```

## Links

- https://megamorf.gitlab.io/cheat-sheets/loki/
