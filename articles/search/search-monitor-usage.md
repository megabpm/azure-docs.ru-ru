---
title: "Мониторинг использования и статистики в службе поиска Azure | Документация Майкрософт"
description: "Отслеживайте потребление ресурсов и размера индексов для Поиска Azure, размещенной облачной службы поиска в Microsoft Azure."
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/01/2017
ms.author: betorres
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 16cb5a1e16a59200f0e731622398efcf24c3f777
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="monitoring-an-azure-search-service"></a>Мониторинг службы поиска Azure

Поиск Azure предлагает различные ресурсы для отслеживания использования и производительности служб поиска. Он предоставляет доступ к метрикам, журналам, статистике индексации и расширенным возможностям мониторинга в Power BI. В этой статье описывается включение различных стратегий мониторинга и интерпретация полученных данных.

## <a name="azure-search-metrics"></a>Метрики Поиска Azure
Метрики позволяют почти в реальном времени отслеживать службу поиска, и они доступны для каждой службы без дополнительной настройки. Они позволяют отслеживать производительность службы в течение 30 дней.

Поиск Azure собирает данные для трех различных метрик:

* Задержка поиска: время, потребовавшееся службе поиска для обработки запросов поиска (данные агрегируются поминутно).
* Запросов поиска в секунду: число запросов поиска, получаемых в секунду (данные агрегируются поминутно).
* Процент регулируемых поисковых запросов: процент отрегулированных запросов поиска (данные агрегируются поминутно).

![Снимок экрана. Число запросов в секунду][1]

### <a name="set-up-alerts"></a>Настройка оповещений
На странице сведений о метрике можно настроить оповещения для отправки уведомлений по электронной почте или автоматических действий, когда метрика превышает заданное пороговое значение.

Дополнительные сведения о метриках см. в полной документации по Azure Monitor.  

## <a name="how-to-track-resource-usage"></a>Мониторинг использования ресурсов
Отслеживание роста индексов и размера документа может помочь заранее настроить емкость перед достижением верхнего предела, установленного для службы. Это можно сделать на портале или программным путем с помощью API-интерфейса REST.

### <a name="using-the-portal"></a>С помощью портала

Для отслеживания использования ресурсов просматривайте счетчики и статистику для службы на [портале](https://portal.azure.com).

1. Войдите на [портал](https://portal.azure.com).
2. Откройте служебную панель службы поиска Azure. Плитки для службы можно найти на домашней странице или загрузить их в службу, нажав «Обзор» на панели быстрого перехода.

Раздел «Использование» включает индикатор, показывающий, какая часть доступных в настоящее время ресурсов используется. Сведения об ограничениях индексов, документов и объема хранилища для каждой службы см. в статье [Ограничения поиска Azure](search-limits-quotas-capacity.md).

  ![Плитка "Использование"][2]

> [!NOTE]
> На снимке экрана представлена бесплатная служба, которая может содержать максимум одну реплику и одну секцию, только 3 индекса, 10 000 документов или 50 МБ данных в зависимости от того, какое событие произойдет первым. Службы, созданные на уровне "Базовый" или "Стандартный", предоставляют значительно больше возможностей. Дополнительные сведения о выборе ценовой категории см. в статье [Выбор SKU или ценовой категории для службы поиска Azure](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Использование REST API
И API REST и пакет SDK для .NET службы поиска Azure обеспечивают программный доступ к показателям обслуживания.  Если вы используете [индексаторы](https://msdn.microsoft.com/library/azure/dn946891.aspx) для загрузки индекса из базы данных SQL Azure или Azure Cosmos DB, для получения требуемых показателей имеется дополнительный API-интерфейс.

* [Получение статистических данных индекса](/rest/api/searchservice/get-index-statistics)
* [Подсчет документов](/rest/api/searchservice/count-documents)
* [Получение состояния индексатора](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Экспорт журналов и метрик

Журналы операций службы и необработанные данные для метрик, описанные в предыдущем разделе, можно экспортировать. Журналы операций позволяют узнать, как используется служба, и их можно использовать из Power BI при копировании данных в учетную запись хранения. С этой целью Поиск Azure предлагает пакет Power BI для мониторинга содержимого.


### <a name="enabling-monitoring"></a>Включение мониторинга
Откройте службу Поиск Azure на [портале Azure](http://portal.azure.com) в разделе "Включение мониторинга".

Выберите данные, которые требуется экспортировать: журналы, метрики или и то и другое. Данные можно скопировать в учетную запись хранения, отправить их в концентратор событий или экспортировать в Log Analytics.

![Включение мониторинга на портале][3]

Сведения о включении мониторинга с помощью PowerShell или Azure CLI см. в документации [здесь](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Журналы и схемы метрик
При копировании в учетную запись хранения данные представляются в формате JSON и размещаются в двух контейнерах:

* insights-logs-operationlogs: для поиска журналов трафика
* insights-metrics-pt1m: для метрик

Для каждого часа и каждого контейнера создается один большой двоичный объект.

Пример пути: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Схема журнала
Большие двоичные объекты журналов содержат журналы трафика службы поиска.
Каждый большой двоичный объект имеет один корневой объект под названием **records** , который содержит массив объектов журнала.
Каждый большой двоичный объект содержит записи по всем операциям, которые выполнялась в течение одного часа.

| Имя | Тип | Пример | Примечания |
| --- | --- | --- | --- |
| time |datetime; |"2015-12-07T00:00:43.6872559Z" |Метка времени операции |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Идентификатор вашего ресурса |
| operationName |string |"Query.Search" |Имя операции |
| operationVersion |string |"2015-02-28" |Используемая версия API |
| category |string |"OperationLogs" |константа |
| resultType |string |Success |Возможные значения: Success или Failure |
| resultSignature |int |200 |Код результата HTTP |
| durationMS |int |50 |Время выполнения операции в миллисекундах |
| properties |object |См. следующую таблицу |Объект, содержащий данные об операции |

**Схема свойств**
| Имя | Тип | Пример | Примечания |
| --- | --- | --- | --- |
| Описание |string |"GET /indexes('content')/docs" |Конечная точка операции |
| Запрос |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Параметры запроса |
| Документы |int |42 |Количество обработанных документов |
| IndexName |string |"testindex" |Имя индекса, связанного с операцией |

#### <a name="metrics-schema"></a>Схема метрик
| Имя | Тип | Пример | Примечания |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Идентификатор вашего ресурса |
| metricName |string |"Latency" |имя метрики |
| time |datetime; |"2015-12-07T00:00:43.6872559Z" |метка времени операции |
| average |int |64 |Среднее количество необработанных выборок в течение интервала времени метрики |
| minimum |int |37 |Минимальное количество необработанных выборок в течение интервала времени метрики |
| maximum |int |78 |Максимальное количество необработанных выборок в течение интервала времени метрики |
| total |int |258 |Общее количество необработанных выборок в течение интервала времени метрики |
| count |int |4 |Количество необработанных выборок, использованных для создания метрики |
| timegrain |string |"PT1M" |Интервал времени в формате метрики ISO 8601 |

Все метрики передают данные с интервалом в одну минуту. Каждая метрика отражает минимальное, максимальное и среднее значения за минуту.

Для метрики SearchQueriesPerSecond минимальным является наименьшее значение числа поисковых запросов в секунду, которое было зарегистрировано за эту минуту. То же относится и к максимальному значению. Средним является совокупное значение за всю минуту.
Представьте такой сценарий: в течение одной минуты может быть одна секунда высокой загрузки, определяющая максимальное значение для SearchQueriesPerSecond, за которой следует 58 секунд средней загрузки, и наконец — одна секунда всего с одним запросом, который определяет минимальное значение.

Для ThrottledSearchQueriesPercentage минимальное, максимальное, среднее и общее значения одинаковы, так как это процент запросов поиска, которые были отрегулированы, от общего числа запросов поиска за одну минуту.

## <a name="analyzing-your-data-with-power-bi"></a>Анализ данных с помощью Power BI

Рекомендуем использовать [Power BI](https://powerbi.microsoft.com) для изучения и визуализации данных. Вы можете легко подключить его к вашей учетной записи хранения Azure и приступить к анализу данных.

Поиск Azure предлагает [пакет содержимого Power BI](https://app.powerbi.com/getdata/services/azure-search), который позволяет отслеживать и анализировать поисковый трафик с помощью заданных диаграмм и таблиц. Пакет содержит набор отчетов Power BI, которые автоматически подключаются к данным и визуально представляют сведения о службе поиска. Дополнительные сведения см. на [странице справки по пакету содержимого](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Панель мониторинга Power BI для Поиска Azure][4]

## <a name="next-steps"></a>Дальнейшие действия
Руководство [Ограничения поиска Azure](search-limits-quotas-capacity.md) содержит рекомендации о том, как сбалансировать выделение секций и реплик для существующей службы.

Более подробные сведения об администрировании службы поиска см. в статье [Администрирование службы поиска Azure на портале Azure](search-manage.md), а рекомендации по настройке в статье [Рекомендации по производительности и оптимизации Поиска Azure](search-performance-optimization.md).

Узнайте больше о создании удивительных отчетов. См. статью [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png

