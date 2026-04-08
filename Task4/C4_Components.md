# C4 Component Diagram — ABS Integration Layer для передачи ставок

Диаграмма компонентов (C4 Level 3) раскрывает внутреннее устройство ABS Integration Layer в части функциональности передачи ставок: REST API для внутреннего КЦ (подзадача A) и файловая выгрузка для партнёрского КЦ (подзадача B). Источник правды — [ADR_Call_Center_Rates.md](ADR_Call_Center_Rates.md).

```plantuml
@startuml C4_Components_Call_Center_Rates
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title C4 Компоненты: ABS Integration Layer (расширение для кол-центров)

Container_Boundary(abs_il, "ABS Integration Layer (Java Spring Boot)") {
    Component(rates_api, "Rates API Controller", "REST Controller", "GET /api/v1/rates — возвращает актуальные ставки для CRM (UC-5)")
    Component(rates_cache, "Rates Cache Service", "Service", "Кэширование матрицы ставок из Oracle DB, обновление по расписанию")
    Component(export_job, "Daily Export Job", "Scheduled Task", "Ежедневное задание: формирование CSV из матрицы ставок (UC-3)")
    Component(crypto_service, "Crypto Service", "Service", "Шифрование файла (PGP/S-MIME) перед отправкой")
    Component(smtp_client, "SMTP Client", "Service", "Отправка зашифрованного файла на email партнёра")
    Component(delivery_monitor, "Delivery Monitor", "Service", "Мониторинг доставки: проверка отправки, логирование, алертинг при сбоях")
}

ContainerDb(abs_db, "ABS Database", "Oracle", "Матрица ставок, данные о депозитах")
Container(cc_crm, "CRM Кол-центра", "Java Spring Boot", "Внутренняя CRM-система")
System_Ext(smtp_server, "SMTP-сервер", "Почтовый сервер банка")
System_Ext(partner_cc, "Система Партнёрского КЦ", "Внешняя система")

Rel(cc_crm, rates_api, "GET /api/v1/rates (HTTPS)")
Rel(rates_api, rates_cache, "Чтение из кэша")
Rel(rates_cache, abs_db, "SELECT из матрицы ставок (по расписанию)")

Rel(export_job, abs_db, "SELECT актуальных ставок")
Rel(export_job, crypto_service, "Передача CSV для шифрования")
Rel(crypto_service, smtp_client, "Передача зашифрованного файла")
Rel(smtp_client, smtp_server, "Отправка email с вложением")
Rel(smtp_server, partner_cc, "Доставка письма")
Rel(smtp_client, delivery_monitor, "Логирование результата отправки")

SHOW_LEGEND()
@enduml
```
