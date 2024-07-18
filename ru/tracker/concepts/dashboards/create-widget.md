---
sourcePath: ru/tracker/api-ref/concepts/dashboards/create-widget.md
---
# Создать виджет «Время цикла»

Запрос позволяет добавить виджет с [диаграммой «Время цикла»](../../user/cycle-time.md) на уже существующий дашборд.

## Формат запроса {#query}

Перед выполнением запроса [получите доступ к API](../access.md).

Чтобы создать виджет, используйте HTTP-запрос с методом `POST`. Параметры запроса передаются в его теле в формате JSON.

```json
POST /{{ ver }}/dashboards/<идентификатор_дашборда>/widgets/cycleTime
Host: {{ host }}
Authorization: OAuth <OAuth-токен>
{{ org-id }}

{
    "description": "<название_виджета>",
    "query" | "filter" | "filterId" : "<фильтр задач>",
    "fromStatuses": [
        {
            "key": "<начальный_статус>"
        }
    ],
    "toStatuses": [
        {
            "key": "<конечный_статус>"
        }
    ],
    "excludedStatuses": [<исключаемые_статусы>],
    "includedStatuses": [<включаемые_статусы>],
    "bucket": {
        "unit": "<период_группировки>",
        "count": <количество_периодов>
    },
    "calendar": <идентификатор_календаря>,
    "lines": {
        "movingAverage": <true> | <false>,
        "standardDeviation": <true> | <false>,
        "percentile": [75, 83, 90],
        "cakePercentile": 85
    },
    "start": "now()-2w",
    "end": "now()-2d",
    "mode": "<режим_отображения_данных>",
    "autoUpdatable": <true> | <false>
}
```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

{% cut "Параметры тела запроса" %}

Тело запроса содержит параметры виджета с диаграммой «Время цикла».

**Обязательные параметры**

Параметр | Описание | Тип данных
-------- | -------- | ----------
description | Название виджета. | Строка

**Дополнительные параметры**

Параметр | Описание | Тип данных
-------- | -------- | ----------
query | Фильтр задач на [языке запросов](../../user/query-filter.md). | Строка
filter | Фильтр задач в формате `{"<поле>": "<значение>"}`. | Строка
filterId | Идентификатор [сохраненного фильтра](../../user/create-filter.md). | Строка
fromStatuses | Ключ статуса, с которого началась работа над задачей. Время в указанном статусе не учитывается. По умолчанию расчет начинается с первого статуса в истории задачи. | Массив
toStatuses | Ключ статуса, на котором работа над задачей завершилась. Если задано несколько статусов, то будет использован самый поздний, в который переходила задача. По умолчанию расчет осуществляется до последнего статуса в истории задачи. | Массив
excludedStatuses | Статусы, время нахождения в которых необходимо убрать из расчетов. | Массив
includedStatuses | статусы, время нахождения в которых необходимо добавить в расчеты. | Массив
[bucket](#bucket) | Величина шага. Значение по умолчанию — 7 дней. | Объект
calendar | Идентификатор [календаря](../../manager/schedule.md) для учета только рабочего времени. Если не задан, используется обычный календарь. | Число
[lines](#lines) | Настройки отображения оси времени. | Объект
start | Формула для начала расчета. Например, расчет за две последние недели: `now() - 2w`. Значение по умолчанию — 2 года. | Строка
end | Формула для окончания расчета. Например, для окончания расчета за два дня до текущей даты: `now() - 2d`. Значение по умолчанию — `now()`.| Строка
mode | Режим отображения данных: <ul><li>`common-lines` — показывать выбранные графики (перцентиль, стандартное отклонение, скользящее среднее);</li><li>`common-lines-and-points` — показывать для выбранных графиков точки, соответствующие задачам;</li><li>`status-lines` — показывать выбранные графики для каждого из статусов.</li> | Строка
autoUpdatable | Признак автоматического обновления диаграммы. | Логический

**Поля объекта** `bucket` {#bucket}

Параметр | Описание | Тип данных
-------- | -------- | ----------
unit | Период группировки дат: <ul><li>`days` — дни;</li><li>`weeks` — недели;</li><li>`months` — месяцы;</li><li>`sprints` — спринты.</li></ul> | Строка
count | Количество периодов с выбранной группировкой. Если в поле `unit` выбрано `sprints`, то количество периодов всегда равно 1. | Число
boardId | Идентификатор доски задач. Доступно, если в поле `unit` выбрано `sprints`. | Строка

**Поля объекта** `lines` {#lines}

Параметр | Описание | Тип данных
-------- | -------- | ----------
movingAverage | Скользящее среднее. Опция, которая добавляет на график линию усредненного значения времени. С ее помощью можно увидеть, как задача проходит цикл по медиане. | Логический
standardDeviation | Стандартное отклонение. Опция, которая добавляет на график полосу среднеквадратичного отклонения. С ее помощью вы можете оценить, насколько широко значения рассеяны от среднего значения. | Логический
percentile | Процент значений в выборке данных, относительно которого вычисляется перцентиль. | Массив
cakePercentile | Значение перцентиля для построения диаграммы, в которой каждый статус отображается отдельно. | Число

{% endcut %}

## Формат ответа {#answer}

{% list tabs %}

- Запрос выполнен успешно

    {% include [answer-201](../../../_includes/tracker/api/answer-201.md) %}

    Тело ответа содержит JSON-объект с параметрами нового виджета.

    ```json
    {
        "id": 123456,
        "version": 1,
        "createdBy": {
            "self": "https://{{ host }}/v2/users/12********",
            "id": "44********",
            "display": "Иван Иванов",
            "passportUid": 12********
        },
        "description": "Диаграмма «Время цикла»",
        "color": 0,
        "dashboard": {
            "self": "https://{{ host }}/v2/dashboards/118899",
            "id": "118899",
            "display": "Дашборд"
        },
        "fromStatuses": [
            {
                "self": "https://{{ host }}/v2/statuses/1",
                "id": "1",
                "key": "open",
                "display": "Открыт"
            }
        ],
        "toStatuses": [
            {
                "self": "https://{{ host }}/v2/statuses/3",
                "id": "3",
                "key": "closed",
                "display": "Закрыт"
            }
        ],
        "bucket": {
            "type": "days",
            "count": 2
        },
        "calendar": {
            "id": "1",
            "display": "Москва. Будни, 11:00−20:00"
        },
        "query": "Queue: TEST Assignee: me()",
        "datasetInfo": {
            "status": "created",
            "buildStartedAt": "2024-04-15T20:58:07.957+0000",
            "builtBy": {
                "self": "https://{{ host }}/v2/users/12********",
                "id": "44********",
                "display": "Иван Иванов",
                "passportUid": 12********
            },
        },
        "lines": {
            "standardDeviation": true,
            "movingAverage": true,
            "percentile": [
                83.0,
                90.0,
                75.0
            ],
            "cakePercentile": 85.0
        },
        "start": "now()-2w",
        "end": "now()-2d",
        "mode": "common-lines-and-points",
        "self": "https://{{ host }}/v2/widgets/123456"
    }
    ```

    {% cut "Параметры ответа" %}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    id | Идентификатор виджета. | Число
    version | Версия виджета. Каждое изменение виджета увеличивает номер версии. | Число
    [createdBy](#user) | Объект с информацией о создателе виджета. | Объект
    description | Название виджета. | Строка
    color | Служебный параметр. | Число
    [dashboard](#dashboard) | Объект с информацией о дашборде, на котором размещен виджет. | Объект
    [fromStatuses](#statuses) | Объект с информацией о статусе, с которого началась работа над задачей. | Объект
    [toStatuses](#statuses) | Объект с информацией о статусе, на котором работа над задачей завершилась. | Объект
    [bucket](#bucket-answer) | Объект с информацией о величине шага. | Объект
    [calendar](#calendar) | Объект с информацией о [графике рабочего времени](../../manager/schedule.md). | Объект
    query \| filter \| filterId | Фильтр задач в выбранном формате. | Строка
    [datasetInfo](#datasetInfo) | Объект с информацией о расчете. | Объект
    [lines](#lines) | Объект с информацией об оси времени. | Объект
    start | Формула для начала расчета. | Строка
    end | Формула для окончания расчета. | Строка
    mode | Режим отображения данных. | Строка
    self | Адрес ресурса API, который содержит параметры виджета. | Строка

    **Поля объекта** `createdBy` и `builtBy` {#user}

    {% include [user](../../../_includes/tracker/api/user.md) %}
    
    **Поля объекта** `dashboard` {#dashboard}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    self | Адрес ресурса API, который содержит параметры дашборда. | Строка
    id | Идентификатор дашборда. | Строка
    display | Название дашборда. | Строка

    **Поля объекта** `bucket` {#bucket-answer}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    type | Период группировки дат: <ul><li>`days` — дни;</li><li>`weeks` — недели;</li><li>`months` — месяцы;</li><li>`sprints` — спринты.</li></ul> | Строка
    count | Количество периодов с выбранной группировкой.  Если в поле `unit` выбрано `sprints`, то количество периодов всегда равно 1.| Число
    boardId | Идентификатор доски задач. Доступно, если в поле `unit` выбрано `sprints`. | Строка

    **Поля объекта** `calendar` {#calendar}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    id | Идентификатор графика рабочего времени. | Строка
    display | Название графика рабочего времени. | Строка

    **Поля объекта** `datasetInfo` {#datasetInfo}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    status | Статус расчета. | Строка
    buildStartedAt | Дата и время запуска расчета в формате: ```YYYY-MM-DDThh:mm:ss.sss±hhmm```.  | Строка
    [builtBy](#user) | Объект с информацией о пользователе, создавшем виджет. | Объект

    **Поля объекта** `lines` {#lines}

    Параметр | Описание | Тип данных
    -------- | -------- | ----------
    standardDeviation | Признак отображения графика стандартного отклонения. | Логический
    movingAverage | Признак отображения графика скользящего среднего. | Логический
    percentile | Список значений в выборке данных в процентах, относительно которых вычисляется перцентиль. | Массив
    cakePercentile | Значение перцентиля для построения диаграммы, в которой каждый статус отображается отдельно. | Число

    {% endcut %}

- Запрос выполнен с ошибкой

    Если запрос не был успешно обработан, API возвращает ответ с кодом ошибки:

    {% include [answer-error-400](../../../_includes/tracker/api/answer-error-400.md) %}

    {% include [answer-error-403](../../../_includes/tracker/api/answer-error-403.md) %}

    {% include [answer-error-404](../../../_includes/tracker/api/answer-error-404.md) %}

    {% include [answer-error-422](../../../_includes/tracker/api/answer-error-422.md) %}

    {% include [answer-error-500](../../../_includes/tracker/api/answer-error-500.md) %}

    {% include [answer-error-503](../../../_includes/tracker/api/answer-error-503.md) %}

{% endlist %}