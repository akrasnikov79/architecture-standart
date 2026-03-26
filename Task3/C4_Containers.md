# C4 Container Diagram (Уровень Контейнеров)

```mermaid
C4Container
    title C4 Контейнеры: Детализация Интернет-Банка и АБС

    Person(client, "Клиент банка", "Действующий клиент")
    Person(managerBO, "Менеджер Бэк-офиса", "Сотрудник депозитного отдела")

    System_Boundary(ib, "Интернет-Банк (Система)") {
        Container(ib_spa, "Web Application", "SPA", "Frontend часть ИБ пользователя")
        Container(ib_api, "API Gateway / BFF", "API Gateway", "Единая точка входа для фронтенда, маршрутизация")
        Container(ib_legacy, "IB Monolith", "Web Server", "Унаследованный функционал (платежи)")
        Container(dep_service, "Deposit Microservice", "Microservice", "Новый микросервис для витрины депозитов и заявок (MVP)")
        ContainerDb(dep_db, "Deposit DB", "Relational Database", "Хранение кэша ставок, справочников и статусов заявок ИБ")
    }

    System_Boundary(abs, "АБС (Система)") {
        Container(abs_api, "ABS Integration Layer", "Integration Service", "API для внешних систем (очередь заявок)")
        Container(abs_core, "ABS Core", "Core System", "Ядро процессинга")
        ContainerDb(abs_db, "ABS Database", "Relational Database", "Главная БД банка")
        Container(abs_client, "ABS Desktop Client", "Desktop App", "Рабочее место сотрудника (Бэк-офис)")
    }

    System_Ext(sms, "СМС-Шлюз Tele2", "Внешняя система")

    %% Связи клиента и ИБ
    Rel(client, ib_spa, "Управление счетами, открытие депозита")
    Rel(ib_spa, ib_api, "Вызов API операций")
    Rel(ib_api, ib_legacy, "Маршрутизация legacy")
    Rel(ib_api, dep_service, "Маршрутизация депозитов")
    
    %% Логика внутри ИБ
    Rel(dep_service, dep_db, "Чтение справочников, запись заявок")
    
    %% Связи ИБ с внешним миром
    Rel(dep_service, abs_api, "Публикация заявки в очередь (Async)")
    Rel(dep_service, sms, "Запрос отправки пароля")
    
    %% Логика внутри АБС
    Rel(abs_api, abs_core, "Передача событий в ядро")
    Rel(abs_core, abs_db, "Чтение/Логирование")
    Rel(managerBO, abs_client, "Утверждение заявки (MVP)")
    Rel(abs_client, abs_db, "Запись статуса депозита")
    Rel(abs_core, sms, "Оповещение об открытии")
```
