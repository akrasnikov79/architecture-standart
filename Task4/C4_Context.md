@startuml C4_Context_Call_Center_Rates
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/v2.0.1/C4_Context.puml

title C4 Контекст: Передача ставок в кол-центры

Person(operatorCC, "Оператор внутреннего КЦ", "Консультирует клиентов по ставкам депозитов")
Person(operatorPartner, "Оператор партнёрского КЦ", "Консультирует клиентов по скрипту с актуальными ставками")
Person(client, "Клиент банка", "Звонит для уточнения условий депозита")

System(abs, "АБС", "Хранение матрицы ставок, ежедневная выгрузка")
System(cc_crm, "Система Кол-центра (CRM)", "Внутренняя CRM: карточки обращений, отображение ставок онлайн")
System_Ext(partner_cc, "Система Партнёрского КЦ", "Внешняя система: импорт ставок из файлов")
System_Ext(smtp, "SMTP-сервер", "Почтовый сервер банка для отправки файлов")

Rel(client, operatorCC, "Звонок в КЦ банка")
Rel(client, operatorPartner, "Звонок в партнёрский КЦ")
Rel(operatorCC, cc_crm, "Просмотр ставок, работа с обращениями")
Rel(operatorPartner, partner_cc, "Просмотр ставок, консультация по скрипту")

Rel(abs, cc_crm, "REST API: актуальные ставки онлайн (UC-5)")
Rel(abs, smtp, "Ежедневная отправка зашифрованного CSV (UC-3)")
Rel(smtp, partner_cc, "Email с вложением (PGP/S-MIME)")
 
@enduml
