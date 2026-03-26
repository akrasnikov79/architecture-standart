  
sequenceDiagram
    actor Client as Клиент
    actor CallCenter as Сотрудник Кол-центра
    actor Front as Сотрудник Фронт-офиса
    actor BackDep as Бэк-офис (Депозиты)
    actor BackCred as Бэк-офис (Кредиты)

    box Системы
        participant CRM as Система Кол-центра
        participant ABS as АБС (Oracle/Delphi)
        participant SMS as СМС-шлюз
        participant Mail as Корпоративная почта
    end

    %% Процесс предварительной заявки
    Client->>CallCenter: Звонок для уточнения деталей
    CallCenter->>CRM: Заводит обращение
    CRM->>ABS: Передача обращения
    
    %% Обработка заявки бэк-офисом
    ABS->>BackDep: Поступает заявка из кол-центра
    BackDep->>BackCred: Запрос уровня кредитного риска (согласование спец. ставки)
    BackCred->>ABS: Анализ риска по клиенту
    BackCred-->>BackDep: Ответ (уровень риска / ставка)
    Note over BackDep: Ручной расчет ставки в Excel
    BackDep->>ABS: Обработка заявки и указание рассчитанной ставки
    ABS->>SMS: Команда на отправку СМС
    SMS-->>Client: СМС: "Вам одобрен депозит под ставку X%"
    
    %% Визит в отделение
    Client->>Front: Личный визит в отделение
    Alt Без предварительного звонка
        Front->>Mail: Письмо в бэк-офис
        Mail->>BackDep: Запрос ставки
        BackDep->>Mail: Ответ со ставкой
        Mail-->>Front: Ставка для клиента
    End
    
    Note over Front, Client: Обсуждение условий, согласие клиента
    Front->>ABS: Создание депозита
    Note over Front, Client: Подписание бумажных документов
    Front->>ABS: Загрузка подписанных документов
    Note over Front, Client: Депозит открыт (Ожидание 20-60 мин)
 
