---
title: "Выполнение запросов аналитики к нескольким базам данных SQL Azure | Документация Майкрософт"
description: "Выполнение распределенных запросов в нескольких базах данных SQL Azure"
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
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5331f9a7b46f1dd31d4aa246ad9d188b5a5afc19
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017


---
# <a name="run-distributed-queries-across-multiple-azure-sql-databases"></a>Выполнение распределенных запросов в нескольких базах данных SQL Azure

В этом руководстве вы выполните запросы аналитики ко всем клиентам в каталоге. Создается задание обработки эластичных баз данных, выполняющее запросы. Задание извлекает данные и загружает их в отдельную базу данных аналитики, созданную на сервере каталога. Эту базу данных можно запрашивать для получения сведений, которые скрыты в текущих операционных данных всех клиентов. В качестве выходных данных задания в базе данных аналитики клиента создается таблица из запросов, возвращающих результат.


Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных аналитики клиента.
> * Создание запланированного задания для получения данных и заполнения базы данных аналитики.

Для работы с этим руководством выполните следующие предварительные требования:

* Разверните приложение SaaS Wingtip. Чтобы развернуть его менее чем за пять минут, см. сведения в статье [Развертывание и изучение мультитенантного приложения SaaS, использующего базу данных SQL Azure](sql-database-saas-tutorial.md).
* Установите Azure PowerShell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Установите последнюю версию SQL Server Management Studio (SSMS). [Download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (Скачивание SQL Server Management Studio (SSMS))

## <a name="tenant-operational-analytics-pattern"></a>Шаблон операционной аналитики клиента

Одной из основных возможностей приложений SaaS является использование форматированных данных клиента, хранящихся в облаке. С помощью этих данных можно проанализировать работу и использование приложения и клиентов. Они могут помочь в разработке функций, повысить удобство использования, а также обеспечить вложение дополнительных средств в приложение и платформу. Доступ к этим данным получить очень просто, если они содержатся в отдельной мультитенантной базе данных, но не так просто, если они распределены по тысячам масштабируемых баз данных. Один из способов доступа к таким данным заключается в использовании заданий обработки эластичных баз данных, которые позволяют записать результаты выполнения задания запроса, возвращающего результаты, в выходной базе данных и таблице.

## <a name="get-the-wingtip-application-scripts"></a>Получение сценариев приложения Wingtip

Скрипты и исходный код приложения SaaS Wingtip доступны в репозитории GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Указания по скачиванию скриптов SaaS Wingtip см. [здесь](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Развертывание базы данных для результатов аналитики клиента

Для работы с данным руководством необходимо развернуть базу данных для записи результатов выполнения скриптов задания, которая содержит запросы, возвращающие результаты. Для этого давайте создадим базу данных с именем tenantanalytics.

1. Откройте файл ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* в *интегрированной среде скриптов PowerShell* и задайте следующее значение:
   * **$DemoScenario** = **2**, чтобы *развернуть базы данных операционной аналитики*.
1. Нажмите клавишу **F5** для запуска демонстрационного скрипта (который вызывает скрипт *Deploy-TenantAnalyticsDB.ps1*), создающего базу данных аналитики клиента.

## <a name="create-some-data-for-the-demo"></a>Создание данных для демонстрации

1. Откройте файл ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* в *интегрированной среде скриптов PowerShell* и задайте следующее значение:
   * **$DemoScenario** = **1**, чтобы *приобрести билеты на мероприятия во всех местах проведения*.
1. Нажмите клавишу **F5** для запуска скрипта и создания журнала покупок билетов.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Создание запланированного задания для получения аналитики клиента о покупках билетов

Этот скрипт создает задание, чтобы получить сведения о покупке билета клиентами. После объединения данных в одну таблицу можно получить обширные детальные метрики о шаблонах покупки билетов для всех клиентов.

1. Откройте среду SSMS и подключитесь к серверу catalog-&lt;пользователь&gt;.database.windows.net.
1. Откройте файл ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*TicketPurchasesfromAllTenants.sql*.
1. Измените &lt;пользователь&gt;. Используйте имя пользователя, примененное при развертывании приложения SaaS Wingtip, в верхней части скрипта: **sp\_add\_target\_group\_member** и **sp\_add\_jobstep**.
1. Щелкните правой кнопкой мыши, выберите **Подключение** и подключитесь к серверу catalog-&lt;пользователь&gt;.database.windows.net, если вы еще не сделали этого.
1. Убедитесь, что вы подключены к базе данных **jobaccount**, и нажмите клавишу **F5** для запуска скрипта.

* **sp\_add\_target\_group** создает целевую группу с именем *TenantGroup*. Теперь нам требуется добавить целевые элементы.
* **sp\_add\_target\_group\_member** добавляет целевой тип элемента *server*, который считает, что базы данных на сервере (обратите внимание, что сервер customer1-&lt;пользователь&gt; содержит базы данных клиентов) во время выполнения задания должны быть включены в задание.
* **sp\_add\_job** создает еженедельное запланированное задание "Покупка билетов клиентами".
* **sp\_add\_jobstep** создает шаг задания, содержащий текст команды T-SQL для получения сведений о всех покупках билетов клиентами и копирования набора, возвращающего результат, в таблицу с именем *AllTicketsPurchasesfromAllTenants*.
* Остальные представления в скрипте отображают сведения о существовании объектов и отслеживают выполнение задания мониторинга. Обратите внимание на значение из столбца **lifecycle** для мониторинга состояния. Когда состояние изменится на "Завершено", это будет означать, что задание успешно завершено на всех базах данных клиентов и двух дополнительных базах данных, содержащих справочную таблицу.

Успешное выполнение скрипта приведет к тем же результатам:

![results](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Создание задания для получения общего числа покупок билетов клиентами

Этот скрипт создает задание, чтобы получить общее количество покупок билетов клиентами.

1. Откройте среду SSMS и подключитесь к серверу *catalog-&lt;пользователь&gt;.database.windows.net*.
1. Откройте файл ...\\Learning Modules\\Provision and Catalog\\Operational Analytics\\Tenant Analytics\\*Results-TicketPurchasesfromAllTenants.sql*.
1. Измените &lt;пользователь&gt;. Используйте имя пользователя, примененное при развертывании приложения SaaS Wingtip в скрипте, в хранимой процедуре **sp\_add\_jobstep**.
1. Щелкните правой кнопкой мыши, выберите **Подключение** и подключитесь к серверу catalog-&lt;пользователь&gt;.database.windows.net, если вы еще не сделали этого.
1. Убедитесь, что вы подключены к базе данных **tenantanalytics**, и нажмите клавишу **F5** для запуска скрипта.

Успешное выполнение скрипта приведет к тем же результатам:

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** создает еженедельное запланированное задание ResultsTicketsOrders.

* **sp\_add\_jobstep** создает шаг задания, содержащий текст команды T-SQL для получения сведений о всех покупках билетов клиентами и копирования набора, возвращающего результат, в таблицу с именем CountofTicketOrders.

* Остальные представления в скрипте отображают сведения о существовании объектов и отслеживают выполнение задания мониторинга. Обратите внимание на значение из столбца **lifecycle** для мониторинга состояния. Когда состояние изменится на "Завершено", это будет означать, что задание успешно завершено на всех базах данных клиентов и двух дополнительных базах данных, содержащих справочную таблицу.


## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Развертывание базы данных аналитики клиента.
> * Создание запланированного задания для получения аналитических данных для всех клиентов.

Поздравляем!

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Руководства по SaaS WTP базы данных SQL](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md)
