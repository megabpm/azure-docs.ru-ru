---
title: "Как добавить источник событий Центра Интернета вещей в среду Azure Time Series Insights | Документация Майкрософт"
description: "В этом руководстве описывается, как добавить источник события, подключенный к Центру Интернета вещей и к среде Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 72037677fac528ff8174d25b474ca7e70826a7b0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-add-an-iot-hub-event-source"></a>Как создать источник событий Центра Интернета вещей

В этом руководстве описывается, как добавить источник события, считывающий данные из Центра Интернета вещей в среду Time Series Insights, с помощью портала Azure.

## <a name="prerequisites"></a>Предварительные требования

Вы создали Центр Интернета вещей и записываете события в него. Дополнительные сведения о Центрах Интернета вещей см. по адресу <https://azure.microsoft.com/services/iot-hub/>.

> [Групп потребителей] Каждый источник событий Time Series Insights должен иметь собственную выделенную группу потребителей, не используемую другими потребителями. Если несколько модулей чтения используют события из одной группы потребителей, как правило, во всех модулях могут произойти сбои. Дополнительные сведения см. в статье [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Выбор параметра импорта

Параметры источника событий можно ввести вручную или же можно выбрать Центр Интернета вещей из списка доступных.
В инструменте выбора **Параметр импорта** выберите один из следующих вариантов:

* Указать параметры Центра Интернета вещей вручную.
* Использовать Центр Интернета вещей из доступных подписок.

### <a name="select-an-available-iot-hub"></a>Выбор доступного Центра Интернета вещей

В следующей таблице представлены все параметры на вкладке "Новый источник событий" с описанием при выборе доступного Центра Интернета вещей в качестве источника событий:

| Имя свойства | Описание |
| --- | --- |
| Имя источника события | Имя источника события. Это имя должно быть уникальным в среде Time Series Insights.
| Источник | Выберите **Центр Интернета вещей** для создания такого источника событий, как Центр Интернета вещей.
| идентификатор подписки; | Выберите подписку, в которой был создан этот Центр Интернета вещей.
| IoT hub name (Имя Центра Интернета вещей) | Выберите имя Центра Интернета вещей.
| IoT hub policy name (Имя политики Центра Интернета вещей) | Выберите политику общего доступа, которую можно найти на вкладке "Параметры" Центра Интернета вещей. Каждой политике общего доступа присваивается имя, а также для нее задаются разрешения и ключи доступа. Политика общего доступа для источника событий *должна* иметь разрешения на **подключение службы**.
| Группа потребителей Центра Интернета вещей | Группа потребителей для чтения событий из Центра Интернета вещей. Мы настоятельно рекомендуем использовать выделенную группу потребителей для источника событий.

### <a name="provide-iot-hub-settings-manually"></a>Указать параметры Центра Интернета вещей вручную.

В следующей таблице представлены все свойства на вкладке "Новый источник событий" с описанием при вводе параметров вручную:

| Имя свойства | Описание |
| --- | --- |
| Имя источника события | Имя источника события. Это имя должно быть уникальным в среде Time Series Insights.
| Источник | Выберите **Центр Интернета вещей** для создания такого источника событий, как Центр Интернета вещей.
| идентификатор подписки; | Подписка, в которой был создан этот Центр Интернета вещей.
| Группа ресурсов | Подписка, в которой был создан этот Центр Интернета вещей.
| IoT hub name (Имя Центра Интернета вещей) | Имя Центра Интернета вещей. При создании вы также дали Центру Интернета вещей определенное имя.
| IoT hub policy name (Имя политики Центра Интернета вещей) | Политика общего доступа, которую можно создать на вкладке "Параметры" Центра Интернета вещей. Каждой политике общего доступа присваивается имя, а также для нее задаются разрешения и ключи доступа. Политика общего доступа для источника событий *должна* иметь разрешения на **подключение службы**.
| Ключ политики Центра Интернета вещей | Ключ общего доступа, используемый для проверки подлинности при получении доступа к пространству имен служебной шины. Введите первичный или вторичный ключ здесь.
| Группа потребителей Центра Интернета вещей | Группа потребителей для чтения событий из Центра Интернета вещей. Мы настоятельно рекомендуем использовать выделенную группу потребителей для источника событий.

## <a name="next-steps"></a>Дальнейшие действия

1. Добавьте политику доступа к данным в среде ([Предоставление доступа к данным для среды Time Series Insights с помощью портала Azure](time-series-insights-data-access.md)).
1. Получите доступ к своей среде на [портале Time Series Insights](https://insights.timeseries.azure.com).
