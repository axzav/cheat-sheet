# Grafana

Grafana это система визуализации (UI) для различных источников данных

Основные элементы:
- **data sources**
- **dashboards**
- **языки запросов**

**Explore**
Позволяет делать "сырые" запросы к источникам данных

Основные источники:
- loki (logs, язык LogQL)
- Prometheus (метрики, язык PromQL)
- CloudWatch (AWS metrics, logs, alarms)
- Amazon Timestream (time-series база данных в AWS, кастомные метрики)

**Dashboard**
Dashboard - набор панелей (panels).

Каждый dashboard:

- состоит из нескольких панелей
- каждая панель делает запрос к data source
- каждая панель рисует график/таблицу/лог
- дашборд содержит переменные, которые используются в запросах к data source
