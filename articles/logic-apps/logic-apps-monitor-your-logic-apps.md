---
title: "Включение ведения журнала и оповещений, просмотр журнала выполнения, отслеживание ввода и вывода с помощью Azure Logic Apps | Документация Майкрософт"
description: "Мониторинг состояния рабочих процессов приложения логики с помощью ведения журнала, трассировки и просмотра журнала и диагностических данных."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: d6840be7afc05d8d563215e370c59cf41a206e4f
ms.contentlocale: ru-ru
ms.lasthandoff: 03/01/2017

---

# <a name="check-the-performance-and-start-diagnostic-logging-and-alerts-of-your-workflows-in-logic-apps"></a>Проверка производительности и включение ведения журналов и оповещений системы диагностики для рабочих процессов в приложениях логики
После [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md)вы можете просмотреть полный журнал его выполнения на портале Azure.  Вы также можете настроить службы, например систему диагностики Azure и службу оповещений Azure, которые будут отслеживать события в режиме реального времени и предупреждать вас о критических событиях, например если в течение часа более 5 запусков завершаются сбоем.

## <a name="monitor-in-the-azure-portal"></a>Мониторинг на портале Azure
Чтобы просмотреть журнал, выберите **Обзор**, а затем — **Приложения логики**. Отобразится список приложений логики в вашей подписке.  Выберите приложение логики, которое требуется отслеживать.  Отобразится список всех действий и триггеров, выполненных для этого приложения логики.

![Обзор](media/logic-apps-monitor-your-logic-apps/overview.png)

В этой колонке есть несколько полезных разделов:

* **Сводка** включает в себя **Все запуски** и **Журнал триггеров**.
  * **Все запуски** — в этой области перечислены последние выполнения приложения логики.  Щелкните любую строку, чтобы просмотреть сведения о выполнении, или плитку, чтобы вывести больше выполнений.
  * **Журнал триггера** — в этой области перечислены все действия триггера этого приложения логики.  Для действий триггера могут быть указаны такие состояния: "Пропущено" — пропущена проверка на наличие новых данных (например, проверка добавления нового файла к FTP), "Успешно" — данные возвращены для запуска приложения логики, "Сбой" — ошибка в конфигурации.
* **Диагностика** — в этом разделе можно просматривать сведения о среде выполнения и события, а также подписаться на получение [оповещений Azure](#adding-azure-alerts)

> [!NOTE]
> Все сведения о времени выполнения и события шифруются в службе Logic Apps. Они расшифровываются только по запросу представления пользователем. Доступ к этим событиям можно также контролировать с помощью управления доступом на основе ролей Azure (RBAC).
> 
> 

### <a name="view-the-run-details"></a>Просмотр сведений о выполнении
В списке запусков представлены сведения о **состоянии**, **времени начала** и **длительности** конкретного запуска. Щелкните любую строку, чтобы увидеть сведения об этом конкретном запуске.

В представлении мониторинга показан каждый этап выполнения, входные и выходные данные, а также сообщения о произошедших ошибках.

![Запуск и действия](media/logic-apps-monitor-your-logic-apps/monitor-view.png)

Если вам нужны дополнительные сведения, например **идентификатор корреляции** запуска (который может использоваться для REST API), нажмите кнопку **Подробности о запуске**.  Эти подробности включают в себя сведения обо всех шагах, состоянии, а также входных и выходных данных выполнения.

## <a name="azure-diagnostics-and-alerts"></a>Система диагностики и оповещения Azure
Помимо упомянутых выше сведений, предоставленных на портале Azure и в REST API, вы можете настроить использование в приложении логики системы диагностики Azure для получения более подробных сведений и выполнения отладки.

1. Щелкните раздел **Диагностика** в колонке приложения логики.
2. Щелкните **Параметры диагностики**
3. Настройте отправку данных в концентратор событий или учетную запись хранения.
   
    ![Параметры системы диагностики Azure](media/logic-apps-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Добавление оповещений Azure
После настройки диагностики вы можете добавить оповещения Azure, которые будут отправляться при превышении определенных пороговых значений.  В колонке **Диагностика** выберите элемент **Оповещения**, а затем — команду **Добавить оповещение**.  Далее вам будут представлены пошаговые указания по настройке оповещения на основе ряда пороговых значений и данных метрики.

![Метрики оповещений Azure](media/logic-apps-monitor-your-logic-apps/alerts.png)

Вы можете настроить необходимые **условие**, **пороговое значение** и **период**.  Наконец, вы можете настроить электронный адрес, куда будут отправляться уведомления, или объект webhook.  Кроме того, в приложении логики можно использовать [триггер запроса](../connectors/connectors-native-reqres.md), который будет запускаться при оповещении (например, чтобы [опубликовать в Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [отправить текст](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) или [добавить сообщение в очередь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### <a name="azure-diagnostics-settings"></a>Параметры системы диагностики Azure
Каждое из этих событий содержит сведения о приложении логики и событии, например о состоянии.  Ниже приведен пример события *ActionCompleted* .

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

Два наиболее важных свойства для отслеживания и мониторинга — *clientTrackingId* и *trackedProperties*.  

#### <a name="client-tracking-id"></a>Идентификатор отслеживания клиента
Идентификатор отслеживания клиента — это значение, по которому будут сопоставляться события в выполнении приложения логики, включая вложенные рабочие процессы, вызываемые как часть этого приложения.  Если этот идентификатор не указан, то он будет создан автоматически. Вы можете вручную указать его из триггера, передав заголовок `x-ms-client-tracking-id` со значением идентификатора в запрос триггера (триггер запроса, HTTP или webhook).

#### <a name="tracked-properties"></a>Отслеживаемые свойства
Отслеживаемые свойства можно добавить в действия в определении рабочего процесса для отслеживания входных или выходных данных в данных диагностики.  Это можно сделать, если в данных телеметрии нужно отслеживать такие данные, как "Идентификатор заказа".  Чтобы добавить отслеживаемое свойство, добавьте свойство `trackedProperties` в действие.  Отслеживаемые свойства позволяют отслеживать входные и выходные данные только одного действия. Однако можно использовать свойства `correlation` событий, чтобы сопоставлять действия в выполнении.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>Расширение решений
Вы можете использовать эти данные телеметрии из концентратора событий или хранилища в других службах, например [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) и [Power BI](https://powerbi.com), чтобы отслеживать рабочие процессы интеграции в режиме реального времени.

## <a name="next-steps"></a>Дальнейшие действия
* [Распространенные примеры и сценарии использования приложений логики](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Создание шаблона развертывания приложения логики](../logic-apps/logic-apps-create-deploy-template.md)
* [Возможности интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)


