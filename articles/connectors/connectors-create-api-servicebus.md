---
title: "Как использовать соединитель служебной шины Azure в приложениях логики | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключитесь к служебной шине Azure для отправки и получения сообщений. Вы можете выполнять такие действия, как отправка в очередь, отправка в раздел, получение из очереди и получение из подписки."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e5d3301c90adf993b1cba35969dfe4dbeaeab499
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Начало работы с соединителем служебной шины Azure
Подключитесь к служебной шине Azure для отправки и получения сообщений. Вы можете выполнять такие действия, как отправка в очередь, отправка в раздел, получение из очереди и получение из подписки.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Подключение к служебной шине
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать подключение к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Использование триггера служебной шины
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Использование действия служебной шины
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="view-the-swagger"></a>Просмотр Swagger
Ознакомьтесь с [дополнительными сведениями о Swagger](/connectors/servicebus/). 

## <a name="next-steps"></a>Дальнейшие действия
[Создание приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).


