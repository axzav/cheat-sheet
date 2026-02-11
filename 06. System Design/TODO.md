Видео о систем дизайне. Простые, база
https://www.youtube.com/@interviewpen/videos


https://www.youtube.com/watch?v=i53Gi_K3o7I - 20 System Design Concepts Explained in 10 Minutes

Термины:
- файл-сервер / клиент-сервер
- надежность / масштабируемость / производительность систем
- масштабирование (горизонтальное / вертикальное)
- монолиты и микросервисы
- stateless and statefull services
- latence and throughput
- SLA / SLO / SLI
- Data / compute insensive приложения
- Балансировака нагрузки (клиентаская), (l4, l7), (round robin / weighted round robin / least connections / sticky sessions)
- Проксирование (forward / reverse)
- Кеширование
- API (SOAP, REST, gRPC, GraphQL), (under / over fetching)
- Observability (мониторинг, логгирование, трейсинг, непрерывное профилирование, анализ сбоев)

Хранение данных:
- OLAP / OLTP
- Data terention (retention?)
- Поисковые движки
- Реляционные / докуменоориентированные / key-value / графовые / времянных рядов / колоночные / blob storage 
- persistent and in-memory databases
- embedded and single file databases
- indexes (btree / hash / bitmap / spatial / inverted), кластерные/некластерные, покрывающие
- ACID and BASE
- Транзакции (WAL), MVCC, 2PL, Deferrable
- Constraints (NOT NULL, UNIQUE, FOREIGN KEY, CHECK, DEFAULT)
- Хранимые процедуры, триггеры, materialized view
- Изоляция транзакций
- брокеры сообщений
- CDN

Распределенное хранение данных
- Бэкапы
- CAP теорема
- Strong / eventual consistency
- Репликация (синхронная / асинхронная / полу-синхронная), с одним ведущим узлом / с несколькими ведущими узлами / без ведущих узлов, statement based / tow based / mixed, логическая / физическая, pull / push, failover, hot standby
- Replication lag (чтение собственных записей / монотонное чтение / согласованное префиксное чтение)
- Фильтарция репликаций
- Вертикальное и горизонтальное партиционирование
- Шардирование (range based / key based / directory based), client / proxy / coordinator routing
- Перебалансировка virtual buckets
- Resharding (consistent / randezvous hashing)
- Внутренне устройство кластера Kafka
- CDC (Debezium)

Паттерны и приемы проектирования
- CQRS
- Pub / sub
- low-latency and higt-troughput apps
- realtime и пакетная обработка запросов
- map reduce
- service descovery
- push and pull models
- толстый клиент
- тегирование и версионирование кэша
- трехзвенная архитектура
- отложенное выполнение задач
- polling / long polling / streaming
- retries (идемподентность / backoff)
