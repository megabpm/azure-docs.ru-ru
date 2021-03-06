---
title: "Диагностика и устранение неполадок | Документация Майкрософт"
description: "В этом руководстве описывается, как выявлять и устранять неполадки в среде Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4b8d5fdab1744b2db658917f91d6dac05db30d2f
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Диагностика и устранение неполадок в среде Time Series Insights

## <a name="i-dont-see-my-data"></a>Данные не отображаются
Ниже приведены некоторые причины, по которым данные могут не отображаться в среде на [портале Azure Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>Источник события не содержит данные в формате JSON
Сейчас Azure Time Series Insights поддерживает только данные в формате JSON. Примеры JSON см. в разделе [Поддерживаемые формы JSON](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>Во время регистрации источника события вы не предоставили ключ с необходимыми разрешениями
* Для Центра Интернета вещей необходимо указать ключ с разрешением на **подключение службы**.

   ![Разрешение на подключение службы Центра Интернета вещей](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Как показано на предыдущем рисунке, можно использовать политику **iothubowner** или **service**, так как обеим назначено разрешение на **подключение службы**.
* Для концентратора событий необходимо указать ключ с разрешением на **прослушивание**.

   ![Разрешение на прослушивание концентратора событий](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Как показано на предыдущем рисунке, можно использовать политику **read** или **manage**, так как обеим назначено разрешение на **прослушивание**.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>Указанная группа потребителей не является уникальной для Time Series Insights
Во время регистрации для Центра Интернета вещей или концентратора событий нужно указать группу потребителей, которая должна использоваться для чтения данных. Эта группа потребителей не должна быть общей. Иначе базовый концентратор событий случайным образом автоматически отключает один из модулей чтения.

## <a name="i-see-my-data-but-theres-a-lag"></a>Данные отображаются с задержкой
Ниже приведены причины, по которым могут отображаться некоторые данные в среде на [портале Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>Выполняется регулирование среды
Ограничение регулирования применяется на основе типа номера SKU и емкости среды. Все источники событий в среде совместно используют емкость. Если источник событий Центра Интернета вещей или концентратора событий превышает ограничение по объему передаваемых данных, наблюдается регулирование и задержка.

На снимке экрана ниже показана среда Time Series Insights с номером SKU S1 и емкостью 3. Она может принимать 3 миллиона событий в день.

![Текущая емкость номера SKU среды](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Предположим, эта среда принимала сообщения из концентратора событий с определенной скоростью входящих данных, как показано на следующей схеме:

![Пример скорости входящих данных для концентратора событий](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Как показано на схеме, ежедневная частота приема составляет около 67 000 сообщений. При этом передается примерно 46 сообщений в минуту. Если все сообщения концентратора событий свести к одному событию Time Series Insights, станет понятно, что в этой среде ничего не регулируется. Если все сообщения концентратора событий свести к 100 событиям Time Series Insights, каждую минуту будет передаваться по 4600 событий. Среда с номером SKU S1 и емкостью 3 может принимать только 2100 событий в минуту (1 миллион событий в день = 700 событий в минуту на 3 единицы = 2100 событий в минуту). Поэтому из-за регулирования происходит задержка. 

Подробные сведения о логике сведения см. в разделе [Поддерживаемые формы JSON](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Рекомендуемые действия
Чтобы устранить задержку, увеличьте емкость номера SKU среды. Дополнительные сведения см. в статье [Как масштабировать среду Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Вы отправляете архивные данные, что приводит к медленной передаче входящих данных
Если вы подключаете существующий источник событий, скорее всего, в Центре Интернета вещей или концентраторе событий уже есть данные. Поэтому среда начинает извлекать данные, как только сообщения источника событий начинают сохраняться. 

Это поведение по умолчанию, которое невозможно переопределить. Вы можете включить регулирование, поэтому обработка архивных данных может занять некоторое время.

#### <a name="recommended-steps"></a>Рекомендуемые действия
Чтобы устранить задержку, выполните следующие действия.
1. Увеличьте емкость номера SKU до максимально допустимого значения (в этом случае — 10). После увеличения емкости прием данных ускорится. Значение ускорения можно увидеть в таблице доступности на [портале Time Series Insights](https://insights.timeseries.azure.com). Плата взимается за увеличенную емкость.
2. Устранив задержку, уменьшите емкость номера SKU до нормального уровня приема.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Параметр *имени свойства метки времени* источника события не работает
Убедитесь, что имя и значение соответствуют следующим правилам:
* В имени свойства метки времени _учитывается регистр_.
* Так как значение свойства метки времени из источника событий является строкой JSON, оно должно иметь формат _гггг-мм-ддТчч:мм:сс.FFFFFFFK_. Например 2008-04-12T12:53Z.

