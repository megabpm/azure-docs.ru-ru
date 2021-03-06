---
title: "Использование Log Analytics с помощью мультитенантного приложения базы данных SQL | Документация Майкрософт"
description: "Настройка и использование Log Analytics (OMS) с примером приложения базы данных SQL Azure Wingtip Tickets (WTP)"
keywords: "руководство по базе данных sql"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 813a947ce4deb0755b44f4d287e00ae5218abfc4
ms.contentlocale: ru-ru
ms.lasthandoff: 05/23/2017


---
# <a name="setup-and-use-log-analytics-oms-with-the-wtp-sample-saas-app"></a>Настройка и использование Log Analytics (OMS) с примером приложения SaaS WTP

В этом руководстве описывается настройка и использование *Log Analytics([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* с приложением WTP для мониторинга эластичных пулов и баз данных. Это продолжение статьи [Мониторинг производительности примера приложения SaaS WTP](sql-database-saas-tutorial-performance-monitoring.md). Здесь демонстрируется использование *Log Analytics* для расширения функций мониторинга и оповещений, предоставляемых на портале Azure. Служба Log Analytics особенно хорошо подходит для мониторинга и создания оповещений с возможностью масштабирования, так как она поддерживает сотни пулов и сотни тысяч баз данных. Она также обеспечивает единое решение для мониторинга, с помощью которого можно отслеживать интеграцию разных приложений и служб Azure в нескольких подписках Azure.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Установка и настройка Log Analytics (OMS).
> * Использование Log Analytics для мониторинга пулов и баз данных.

Для работы с этим руководством выполните следующие предварительные требования:

* Разверните приложение WTP. Чтобы развернуть его менее чем за пять минут, см. сведения в статье [Развертывание и изучение мультитенантного приложения SaaS, использующего базу данных Azure SQL](sql-database-saas-tutorial.md).
* Установите Azure PowerShell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Обсуждение сценариев и шаблонов SaaS и их влияния на требования к решениям для мониторинга см. в статье [Мониторинг производительности примера приложения SaaS WTP](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Мониторинг производительности и управление ею с помощью Log Analytics (OMS)

Для базы данных SQL средства мониторинга и создания оповещений доступны в базах данных и пулах. Встроенные возможности мониторинга и оповещений ресурса удобно использовать для небольшого количества ресурсов. Они менее подходят для мониторинга крупных сред или обеспечения единого представления для разных ресурсов и подписок.

Log Analytics можно использовать для сценариев с большим количеством операций. Это отдельная служба Azure, которая предоставляет аналитические сведения на основе журналов диагностики и данных телеметрии, собранных в рабочей области Log Analytics. Эта служба может собирать данные телеметрии из многих служб, а также позволяет выполнять запросы и настраивать оповещения. Log Analytics предоставляет встроенный язык запросов и средства визуализации данных, позволяющие выполнять аналитику и визуализацию рабочих данных. Решения служб анализа SQL предоставляют несколько предопределенных представлений и запросов мониторинга и оповещений эластичных пулов и баз данных, а также позволяют добавить собственные ad-hoc запросы и сохранять их при необходимости. OMS также предоставляет конструктор пользовательского представления.

Решения аналитики и рабочих областей Log Analytics можно запустить на порталах Azure и OMS. Портал Azure является новой точкой доступа, однако может уступать порталу OMS в некоторых областях.

### <a name="start-the-load-generator-to-create-data-to-analyze"></a>Запуск генератора нагрузки для создания данных для анализа

1. Откройте **Demo-PerformanceMonitoringAndManagement.ps1** в **интерактивной среде сценариев PowerShell**. Не закрывайте этот скрипт, так как при работе с этим руководством может потребоваться запустить несколько сценариев генератора нагрузки.
1. Если у вас имеется менее пяти клиентов, подготовьте пакет клиентов, чтобы предоставить более содержательный контекст мониторинга:
   1. Чтобы выбрать скрипт **подготовки пакета клиентов**, задайте для параметра **$DemoScenario** значение 1.
   1. Нажмите клавишу **F5** для запуска скрипта.

1. Чтобы **создать обычную нагрузку (около 40 DTU)**, задайте для параметра **$DemoScenario** значение 2.
1. Нажмите клавишу **F5** для запуска скрипта.

## <a name="get-the-wingtip-application-scripts"></a>Получение скриптов приложения Wingtip

Сценарий Wingtip Tickets и исходный код приложения доступны в репозитории GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Файлы сценариев находятся в папке [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Скачайте папку **Learning Modules** на локальный компьютер, сохраняя ее структуру.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Установка и настройка Log Analytics и решений Аналитики SQL Azure

Log Analytics — это отдельная служба, которую необходимо настроить. Log Analytics собирает данные журнала, телеметрию и метрики в рабочей области Log Analytics. Создайте рабочую область, которая ничем не отличается от других ресурсов в Azure. Рабочую область не обязательно создавать в одной и той же группе ресурсов с приложением, которое она отслеживает, однако зачастую это самый оптимальный вариант. В случае с приложением WTP это позволяет легко удалить рабочую область вместе с приложением, просто удалив группу ресурсов.

1. В **интегрированной среде сценариев PowerShell** откройте файл \\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1*.
1. Нажмите клавишу **F5** для запуска скрипта.

На этом этапе можно будет открыть Log Analytics на портале Azure (или на портале OMS). Чтобы собрать данные телеметрии и отобразить их в рабочей области Log Analytics, потребуется несколько минут. Чем дольше выполняется процесс сбора системных данных, тем содержательнее будет результат. Сейчас самое время сделать перерыв. Только убедитесь, что генератор нагрузки все еще работает.


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Использование Log Analytics и решений Аналитики SQL для мониторинга пулов и баз данных


В этом упражнении откройте Log Analytics и портал OMS для просмотра данных телеметрии, собираемых для баз данных и пулов WTP.

1. Перейдите на [портал Azure](https://portal.azure.com) и откройте Log Analytics, щелкнув "Больше служб" и выполнив поиск Log Analytics:

   ![Открытие службы Log Analytics](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. Выберите рабочую область *wtploganalytics-&lt;пользователь&gt;*.

1. Щелкните **Обзор**, чтобы открыть решение Log Analytics на портале Azure.
   ![Ссылка на параметр "Обзор"](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **Важно!** Активация решения может занять несколько минут. Подождите.

1. Щелкните элемент Аналитики SQL Azure, чтобы открыть его.

    ![Обзор](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![Аналитика](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. Используйте полосу прокрутки внизу, чтобы просмотреть колонку решения (при необходимости можно обновить колонку).

1. Просмотрите различные представления (щелкнув их) или выберите отдельные ресурсы, чтобы открыть детальный обозреватель. Воспользуйтесь ползунком с показателем времени в верхнем левом углу или щелкните вертикальную черту, чтобы изучить данные за конкретный временной интервал. В этом представлении можно выбрать отдельные базы данных или пулы, чтобы уделить внимание конкретным ресурсам:

    ![Диаграмма](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. Прокрутите колонку решений вправо, чтобы отобразить некоторые сохраненные запросы. Щелкните, чтобы просмотреть эти запросы. Вы можете поэкспериментировать с их изменением, а затем сохранить интересные сформированные запросы, которые можно будет повторно открыть и использовать с другими ресурсами.

1. В колонке рабочей области Log Analytics выберите портал OMS, чтобы открыть в нем решение.

    ![OMS](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. На портале OMS можно настроить оповещения. Щелкните область оповещений в представлении DTU базы данных.

1. В открывшемся представлении Log Search появится линейная диаграмма представляемой метрики.

    ![Поиск в журналах](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. Если щелкнуть оповещение на панели инструментов, отобразится конфигурация оповещений, которую можно изменить.

    ![Добавление правила оповещения](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

В отличие от создания оповещений для каждого ресурса в колонке для конкретного ресурса, мониторинг и оповещения в Log Analytics и OMS основаны на запросах к данным в рабочей области. Таким образом, вместо того чтобы определять оповещение для каждой базы данных, можно определить одно общее оповещение для всех баз данных или создать оповещение, использующее составной запрос по нескольким типам ресурсов. Запросы ограничиваются только данными, доступными в рабочей области.

За использование Log Analytics для базы данных SQL будет взиматься плата, которая зависит от потребляемого объема данных в рабочей области. В этом руководстве вы создали бесплатную рабочую область с ограничением на 500 МБ в день. При достижении лимита данные больше не будут добавляться в рабочую область.


## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Установка и настройка Log Analytics (OMS).
> * Использование Log Analytics для мониторинга пулов и баз данных.

[Выполнение распределенных запросов в нескольких базах данных SQL Azure](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Руководства по работе с SaaS-приложением WTP, использующим базу данных SQL Azure](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Мониторинг базы данных SQL Azure с помощью служб анализа SQL Azure (предварительная версия) в Log Analytics](../log-analytics/log-analytics-azure-sql.md).
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)

