# Walkthrough: Доработка проекта «architecture-standart»

## Что сделано

### P0 — Критичные доработки

#### 1. Единый ADR для Task 3 по шаблону Яндекса
- **Файл:** [ADR_Deposit_Online.md](file:///f:/project/practicum.yandex/architecture-standart/Task3/ADR_Deposit_Online.md)
- Объединил содержимое из `ADR_1_Deposit_Microservice.md` и `ADR_2_ABS_Async_Integration.md`
- Добавил **5 Use Cases** в табличном формате (витрина в ИБ, подача заявки в ИБ, заявка через сайт, обработка бэк-офисом, уведомление)
- Добавил **10 нефункциональных требований** (SLA 99.9%, кэширование, Kafka, масштабирование и т.д.)
- Встроил все 3 уровня **C4-диаграмм** (Context, Containers, Components) с привязкой к Use Cases
- Описал **3 альтернативы** с pros/cons
- Добавил таблицу **рисков и ограничений**

#### 2. Единый ADR для Task 4 по шаблону Яндекса
- **Файл:** [ADR_Call_Center_Rates.md](file:///f:/project/practicum.yandex/architecture-standart/Task4/ADR_Call_Center_Rates.md)
- Добавил решение для **внутреннего КЦ** (CRM → REST API от ABS Integration Layer) — ранее отсутствовало
- Добавил **5 Use Cases** (консультация во внутреннем КЦ, консультация в партнёрском, ежедневная выгрузка, импорт у партнёра, онлайн-синхронизация)
- Добавил **7 нефункциональных требований**
- Создал **C4 Context** — все участники кейса КЦ
- Создал **C4 Component** — компоненты ABS Integration Layer (Rates API, Export Job, Crypto Service, SMTP Client, Delivery Monitor)
- Описал **3 альтернативы** и таблицу **рисков**

### P1 — Рекомендованные исправления

#### 3. Исправления в Task 1
- **Файл:** [IT_Landscape.md](file:///f:/project/practicum.yandex/architecture-standart/Task1/IT_Landscape.md)
- Исправлена опечатка: «**Отрытие**» → «**Открытие**» в заголовке столбца
- Добавлено примечание о разделении AS-IS и TO-BE состояний

## Оставшиеся пункты

| Пункт | Приоритет | Статус |
|-------|-----------|--------|
| RoadMap в `.drawio` | P1 | Не сделан — Mermaid Gantt оставлен, требует решения пользователя |
| Проверка Integration Scheme на AS-IS | P2 | Не сделан |
| Удаление старых ADR_1, ADR_2, ADR_3 | — | На усмотрение пользователя (новые файлы их заменяют) |
