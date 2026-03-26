# Схема интеграции и бизнес-процесса открытия депозита (BPMN Collaboration)

Диаграмма описывает сквозной бизнес-процесс (уровень Collaboration) с участием Клиента, Кол-центра и Бэк-офиса, демонстрируя ручные шаги и обмены сообщениями между независимыми пулами (свимлайнами).

```mermaid
flowchart TD
    %% Определение стилей
    classDef manualProcess fill:#f9f,stroke:#333,stroke-width:2px;
    classDef systemProcess fill:#bbf,stroke:#333,stroke-width:2px;
    
    subgraph ClientPool [Клиент]
        direction TB
        c_start((Начало)) --> c_search(Поиск предложений \nна сайте/в ИБ)
        c_search --> c_apply(Оставляет \nонлайн заявку)
        c_apply --> c_wait(Ожидание звонка)
        c_wait --> c_visit(Визит в \nотделение)
        c_visit --> c_sign(Подписание \nдоговора)
        c_sign --> c_end((Депозит \nоткрыт))
    end

    subgraph CCPool [Кол-центр]
        direction TB
        cc_receive(Прием лида в CRM)
        cc_call{Звонок клиенту}:::manualProcess
        cc_confirm(Фиксация условий)
        cc_receive --> cc_call
        cc_call -->|Клиент согласен| cc_confirm
        cc_call -->|Отказ| cc_cancel((Отмена))
    end

    subgraph BOPool [Бэк-офис (Менеджеры)]
        direction TB
        bo_receive(Очередь новых заявок)
        bo_calc(Расчет индивидуальной \nставки в Excel):::manualProcess
        bo_approve(Ввод ставки \nи утверждение в АБС):::manualProcess
        bo_receive --> bo_calc --> bo_approve
    end

    subgraph ABSPool [АБС]
        direction TB
        abs_store(Сохранение статуса \n"Заявка создана"):::systemProcess
        abs_update(Статус "Ставка утверждена"):::systemProcess
        abs_open(Создание \nдепозитного счета):::systemProcess
        abs_sms(Отправка команды \nв СМС-шлюз):::systemProcess
        abs_store --> abs_update --> abs_open --> abs_sms
    end

    %% Взаимодействие между пулами (Message Flows)
    c_apply -.->|Отправка формы| cc_receive
    cc_confirm -.->|Передача параметров| bo_receive
    bo_approve -.->|Ручной ввод| abs_update
    abs_store -.->|Push-уведомление| bo_receive
    
    abs_sms -.->|Доставка СМС| c_end
    c_sign -.->|Подтверждение| abs_open
```
