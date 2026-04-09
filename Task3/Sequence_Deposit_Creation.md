# UML Sequence Diagram: Технический процесс подачи заявки онлайн

В диаграмме описывается технический процесс прохождения заявки клиента от Интернет-Банка через Deposit Service в АБС. Показан сценарий асинхронного взаимодействия.

```mermaid
sequenceDiagram
    autonumber
    actor User as Frontend SPA
    participant API as Deposit Microservice (API)
    participant DB as Deposit DB (MS SQL)
    participant Kafka as Message Broker (Kafka)
    participant ABS_IL as ABS Integration Layer (Java)
    participant ABS_DB as ABS Core DB (Oracle)

    User->>API: POST /api/v1/applications {product, amount, otp}
    activate API
    
    API->>API: Валидация подписи (OTP-код)
    API->>DB: Сохранение заявки со статусом NEW
    activate DB
    DB-->>API: applicationID = UUID
    deactivate DB

    API->>Kafka: Publish `abs.deposit.applications.created.v1`
    activate Kafka
    Kafka-->>API: ACK (Опубликовано)
    deactivate Kafka

    API-->>User: 201 Created {applicationId, status: NEW}
    deactivate API

    %% Асинхронная вычитка АБС
    loop Event Polling
        ABS_IL->>Kafka: Poll events
        activate ABS_IL
        Kafka-->>ABS_IL: ApplicationCreatedEvent
        
        ABS_IL->>ABS_DB: INSERT INTO abs_applications_queue
        activate ABS_DB
        ABS_DB-->>ABS_IL: Success
        deactivate ABS_DB

        ABS_IL->>Kafka: Commit Offset
        deactivate ABS_IL
    end

    %% ... (Здесь происходит ручная обработка Бэк-офиса, которая опущена в тех. диаграмме) ...

    %% Обратный вызов статуса
    Note over ABS_DB, ABS_IL: Менеджер БО утвердил ставку
    ABS_IL->>ABS_DB: Чтение измененных статусов
    activate ABS_IL
    ABS_IL->>Kafka: Publish `abs.deposit.applications.status_changed.v1`
    deactivate ABS_IL

    API->>Kafka: Poll events
    activate API
    Kafka-->>API: ApplicationStatusChangedEvent
    API->>DB: UPDATE status = RATE_APPROVED
    API->>Kafka: Commit Offset
    deactivate API
```
