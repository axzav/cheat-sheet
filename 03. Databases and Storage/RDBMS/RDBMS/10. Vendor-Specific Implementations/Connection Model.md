## Процессы и потоки

В постгресс коренной процесс пораждает несколько служебных процессов которые выполняют различные задачи.
Также каждый клиент облуживается отдельным процессом


В майсикюл существует один родительский процесс. Служебные задачи выполняются в потоках. Под каждое клиентское соединие создается отдельный поток. Это значит, что на одноядерной машине мускул будет более производителен

С точки зрения прикладного приложения это значит, что для каждой сессии нужно будет создавать процесс (в случае с постгресс) или поток (в случае в mysql), а также открывать соединение, если сервер базы находится на другой машине. 
Для этой задачи используется Connection Pool.
В mysql это client-side connection pool, в postgress это PGPool-II и/или PgBouncer

------------------------------

Postgres uses process per connection where each connection spawns a new process. MySQL uses thread per connection where each connection spawns a new thread. Thus Postgres provides better isolation, e.g. an invalid memory access bug only crashes a single process instead of the entire database server. On the other hand, the process model consumes more resources. Thus for Postgres production deployment, it's recommended to proxy the connection via a connection pooler such as PgBouncer or pgcat.

