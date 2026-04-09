@startuml C4_Containers_Deposit_Online
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/v2.0.1/C4_Container.puml

title C4 Контейнеры: Детализация Интернет-Банка и АБС

Person(client, "Клиент банка", "Действующий клиент")
Person(managerBO, "Менеджер Бэк-офиса", "Сотрудник депозитного отдела")

System_Boundary(ib, "Интернет-Банк (Система)") {
    Container(ib_spa, "Web Application", "SPA (React.js)", "Frontend-часть ИБ пользователя")
    Container(ib_api, "API Gateway / BFF", "API Gateway", "Единая точка входа, маршрутизация, аутентификация")
    Container(ib_legacy, "IB Monolith", "ASP.NET MVC 4.5", "Унаследованный функционал (платежи, текущие счета)")
    Container(dep_service, "Deposit Microservice", "ASP.NET Core", "Витрина депозитов, приём заявок, кэш ставок (MVP)")
    ContainerDb(dep_db, "Deposit DB", "MS SQL", "Кэш ставок, справочники, статусы заявок ИБ")
}

Container(kafka, "Message Broker", "Apache Kafka", "Асинхронный транспорт между ИБ и АБС")

System_Boundary(abs, "АБС (Система)") {
    Container(abs_api, "ABS Integration Layer", "Java Spring Boot", "Интеграционный шлюз: приём/отправка событий через Kafka")
    Container(abs_core, "ABS Core", "PL/SQL, Delphi", "Ядро процессинга, бизнес-логика")
    ContainerDb(abs_db, "ABS Database", "Oracle", "Главная БД банка")
    Container(abs_client, "ABS Desktop Client", "Delphi", "Рабочее место сотрудника бэк-офиса")
}

System_Ext(sms, "СМС-Шлюз", "Внешняя система телеком-оператора")

Rel(client, ib_spa, "Управление счетами, открытие депозита")
Rel(ib_spa, ib_api, "Вызов API операций")
Rel(ib_api, ib_legacy, "Маршрутизация legacy-запросов")
Rel(ib_api, dep_service, "Маршрутизация запросов по депозитам")

Rel(dep_service, dep_db, "Чтение справочников, запись заявок")
Rel(dep_service, kafka, "Публикация ApplicationCreatedEvent")
Rel(dep_service, sms, "Запрос отправки OTP")

Rel(kafka, abs_api, "Доставка событий заявок")
Rel(abs_api, abs_core, "Передача в ядро для обработки")
Rel(abs_core, abs_db, "Чтение/запись данных")
Rel(managerBO, abs_client, "Утверждение заявки (MVP)")
Rel(abs_client, abs_core, "Работа со ставками и заявками")
Rel(abs_core, kafka, "Публикация ApplicationStatusChangedEvent")
Rel(abs_core, sms, "Оповещение об открытии депозита")

@enduml

