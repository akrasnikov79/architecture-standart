# Domain Data Model

Бизнес-модель описывает структуру ключевых сущностей предметной области "Депозиты", которые будут использоваться в контрактах (OpenAPI / AsyncAPI), БД микросервиса и АБС.

```mermaid
classDiagram
    class Client {
        +String clientId
        +String fullName
        +String phoneNumber
        +Boolean isIdentified
        +RiskProfile riskLevel
    }

    class DepositProduct {
        +String productCode
        +String title
        +Decimal baseInterestRate
        +Decimal minAmount
        +Int maxTermMonths
        +Boolean isActive
    }

    class DepositApplication {
        +String applicationId
        +String clientId
        +String productCode
        +Decimal requestedAmount
        +ApplicationStatus status
        +Decimal approvedRate
        +DateTime createdAt
        +DateTime updatedAt
    }

    class DepositAccount {
        +String accountId
        +String clientId
        +String productCode
        +Decimal currentBalance
        +Decimal currentRate
        +DateTime openedAt
        +DateTime expiresAt
    }

    %% Отношения
    Client "1" -- "*" DepositApplication : submits
    Client "1" -- "*" DepositAccount : owns
    DepositProduct "1" -- "*" DepositApplication : references
    DepositProduct "1" -- "*" DepositAccount : based on
```

## Бизнес-глоссарий (Словари)

**ApplicationStatus** (Статус заявки):
- `NEW` — Создана клиентом в ИБ / на сайте.
- `PENDING_BACKOFFICE` — Ожидает ручной обработки менеджером БО.
- `RATE_APPROVED` — Бэк-офис согласовал ставку.
- `OPENED` — Депозит успешно открыт в АБС.
- `REJECTED` — Отклонена менеджером или АБС.

**RiskProfile** (Профиль кредитного риска для согласования спец-ставок):
- `LOW`
- `MEDIUM`
- `HIGH`
