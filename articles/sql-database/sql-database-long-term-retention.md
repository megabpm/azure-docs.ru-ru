---
title: "Хранение резервных копий базы данных SQL Azure до 10 лет | Документация Майкрософт"
description: "Узнайте, как база данных SQL Azure поддерживает хранение резервных копий в течение 10 лет."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: e47c10c2c979622fba254757d3f7b9f19d3240f8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Хранение резервных копий базы данных SQL Azure до 10 лет
В соответствии с нормативными требованиями, стандартами соответствия или бизнес-задачами многие приложения должны хранить автоматические полные резервные копии баз данных более 7–35 дней, то есть дольше, чем при [автоматическом резервном копировании](sql-database-automated-backups.md) базы данных SQL. Функция **долгосрочного хранения резервных копий** позволяет хранить резервные копии базы данных SQL Azure в хранилище служб восстановления Azure до 10 лет. В каждом хранилище можно разместить до 1000 баз данных. Вы можете выбрать любую резервную копию из хранилища и восстановить ее как новую базу данных.

> [!IMPORTANT]
> Долгосрочное хранение резервных копий в настоящее время находится на этапе предварительной версии и доступно в следующих регионах: восточная Австралия, юго-восточная Австралия, южная Бразилия, центральная часть США, Восточная Азия, восточная часть США, восточная часть США 2, центральная Индия, южная Индия, восточная Япония, западная Япония, северо-центральный регион США, Северная Европа, юго-центральный регион США, Юго-Восточная Азия, Западная Европа и западная часть США.
>

> [!NOTE]
> В каждом хранилище в течение 24 часов можно включить до 200 баз данных. Поэтому мы рекомендуем использовать отдельное хранилище для каждого сервера, чтобы эти ограничения не мешали вам. 
> 



 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>Как работает долгосрочное хранение резервных копий базы данных SQL?

Долгосрочное хранение резервных копий позволяет привязать сервер базы данных SQL Azure к хранилищу служб восстановления Azure. 

* Это хранилище должно быть создано в той же подписке Azure, что и сервер для базы данных SQL, в том же географическом регионе и в той же группе ресурсов. 
* Затем вы сможете настроить политику хранения для любой базы данных. Если эта политика настроена, полная резервная копия базы данных еженедельно копируется в хранилище служб восстановления и хранится там в течение указанного срока (до 10 лет). 
* Вы сможете восстановить любую из этих резервных копий, развернув ее в новую базу данных на любом сервере в вашей подписке. Служба хранилища Azure копирует для этой цели существующую резервную копию, и этот процесс никак не влияет на производительность существующей базы данных.

> [!TIP]
> Практическое руководство см. в статье [Настройка долгосрочного хранения резервных копий для Базы данных SQL Azure и восстановление из резервной копии](sql-database-long-term-backup-retention-configure.md).

## <a name="how-do-i-enable-long-term-backup-retention"></a>Как включить долгосрочное хранение резервных копий?

Чтобы настроить долгосрочное хранение резервной копии для базы данных, выполните следующие действия.

1. Создайте хранилище служб восстановления Azure в том же регионе, той же подписке и той же группе ресурсов, где был создан сервер базы данных SQL. 
2. Зарегистрируйте сервер в хранилище.
3. Создайте политику защиты служб восстановления Azure.
4. Примените политику защиты к базе данных, для которой вам требуется долгосрочное хранение резервных копий.

### <a name="azure-portal"></a>Портал Azure

Чтобы настроить и администрировать долгосрочное хранение создаваемых автоматически резервных копий в хранилище служб восстановления Azure, а также выполнять восстановление с помощью таких резервных копий на портале Azure, щелкните **Долгосрочное хранение архивных копий**, выберите базу данных и нажмите кнопку **Настроить**. 

   ![Выбор базы данных для настройки долгосрочного хранения резервных копий](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

Сведения о настройке и администрировании долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure, а также о восстановлении посредством таких резервных копий с помощью PowerShell см. в разделе [Настройка долгосрочного хранения резервных копий для Базы данных SQL Azure и восстановление из резервной копии](sql-database-long-term-backup-retention-configure.md).

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>Как восстановить базу данных, сохраненную с помощью функции долгосрочного хранения резервных копий?

Для восстановления из резервной копии долгосрочного хранения сделайте следующее.

1. Получите список содержимого хранилища, в котором хранится резервная копия.
2. Получите список содержимого контейнера, который сопоставлен с нужным логическим сервером.
3. Получите список содержимого источника данных в хранилище, которое сопоставлено с нужной базой данных.
4. Получите список точек восстановления, доступных для восстановления.
5. Выполните восстановление из подходящей точки восстановления на целевой сервер в вашей подписке.

Сведения о настройке и администрировании долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure, а также о восстановлении с помощью таких резервных копий на портале Azure см. в разделе [Настройка и администрирование долгосрочного хранения резервных копий баз данных в хранилище служб восстановления Azure и восстановление посредством этих резервных копий с помощью портала Azure](sql-database-long-term-backup-retention-configure.md). Сведения о настройке и администрировании долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure, а также о восстановлении посредством таких резервных копий с помощью PowerShell см. в разделе [Настройка долгосрочного хранения создаваемых автоматически резервных копий баз данных в хранилище служб восстановления Azure с помощью PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="how-much-does-long-term-backup-retention-cost"></a>Сколько стоит долгосрочное хранение резервных копий?

Плата за долгосрочное хранение резервных копий базы данных SQL Azure взимается в соответствии с [ценами на службу архивации Azure](https://azure.microsoft.com/pricing/details/backup/).

С момента регистрации сервера базы данных SQL Azure в хранилище с вас будет взиматься плата за общий объем хранилища, используемый для хранения еженедельных резервных копий.

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>Просмотр доступных резервных копий с длительным периодом удержания

Сведения о настройке и администрировании долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure, а также о восстановлении с помощью таких резервных копий на портале Azure см. в разделе [Настройка и администрирование долгосрочного хранения резервных копий баз данных в хранилище служб восстановления Azure и восстановление посредством этих резервных копий с помощью портала Azure](sql-database-long-term-backup-retention-configure.md). Сведения о настройке и администрировании долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure, а также о восстановлении посредством таких резервных копий с помощью PowerShell см. в разделе [Настройка долгосрочного хранения создаваемых автоматически резервных копий баз данных в хранилище служб восстановления Azure с помощью PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disabling-long-term-retention"></a>Отключение долгосрочного хранения

Служба восстановления автоматически очищает резервные копии в соответствии с настроенной политикой хранения. 

* Чтобы резервные копии определенной базы данных больше не отправлялись в хранилище, удалите политику хранения для этой базы данных.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Это не повлияет на резервные копии, которые уже находятся в хранилище. Они будут автоматически удалены службой восстановления по истечении указанного срока хранения.

## <a name="long-term-backup-retention-faq"></a>Часто задаваемые вопросы о долгосрочном хранении резервных копий

1. Вопрос. Можно ли вручную удалить из хранилища определенные резервные копии?

    Ответ. В настоящее время нет. Хранилище автоматически удаляет резервные копии по истечении периода хранения.
2. Вопрос. Можно ли зарегистрировать сервер в нескольких хранилищах для хранения резервных копий?

    Ответ. Пока нет. Сейчас можно сохранять резервные копии только в одном хранилище.
3. Вопрос. Можно ли использовать хранилище и сервер из разных подписок?

    Ответ. Нет. Сейчас хранилище и сервер должны находиться в одной подписке и в одной группе ресурсов.
4. Вопрос. Можно ли использовать хранилище, созданное не в том регионе, где расположен мой сервер?

    Ответ. Нет, хранилище и сервер должны находиться в одном регионе, чтобы сократить время копирования и избежать расходов на трафик.
5. Вопрос. Сколько баз данных можно хранить в одном хранилище?

    Ответ. В настоящее время поддерживаются не более 1000 баз данных на одно хранилище. 
6. В. Сколько хранилищ можно создать в одной подписке?

    О. В одной подписке можно создать до 25 хранилищ.
7. В. Сколько баз данных можно настроить для одного хранилища в день?

    О. Для одного хранилища можно настроить не более 200 баз данных в день.
8. Вопрос. Работает ли долгосрочное хранение резервных копий с эластичными пулами?

    Ответ. Да. Политику хранения можно настроить для любой базы данных в пуле.
9. Вопрос. Можно ли выбрать время создания резервной копии?

    Ответ. Нет, расписанием архивации управляет база данных SQL. Это нужно для того, чтобы минимизировать влияние на производительность баз данных.
10. Вопрос. Для моей базы данных включено прозрачное шифрование данных. Можно ли использовать прозрачное шифрование данных в хранилище? 

    О. Да, прозрачное шифрование данных поддерживается. Вы сможете восстановить базу данных из хранилища, даже если исходной базы данных уже не существует.
11. В. Что произойдет с резервными копиями в хранилище, если моя подписка будет приостановлена? 

    О. Если подписка приостановлена, мы храним все имеющиеся базы данных и резервные копии. Но новые резервные копии сохраняться в хранилище не будут. Когда вы повторно активируете подписку, служба возобновит сохранение резервных копий в хранилище. Хранилище станет доступным для операций восстановления с использованием тех резервных копий, которые были созданы до приостановки подписки. 
12. Вопрос. Можно ли получить доступ к файлам резервных копий базы данных SQL, чтобы скачать или восстановить их на SQL Server?

    Ответ. В настоящее время нет.
13. Вопрос. Можно ли создать несколько расписаний (ежедневно, еженедельно, ежемесячно, ежегодно) в рамках политики хранения SQL?

    Ответ. Пока нет. Сейчас такая возможность есть только для резервных копий виртуальных машин.
14. В. Что делать при настройке длительного периода удержания резервных копий в базе данных-получателе с активной георепликацией?

    Ответ. В настоящее время резервное копирование на репликах не выполняется, поэтому длительный период удержания резервных копий в базах данных-получателей невозможен. Тем не менее клиенту важно настроить длительный период удержания резервных копий в базе данных-получателе с активной георепликацией по этим причинам:
    - Если при отработке отказа база данных становится источником, создается полная резервная копия, которая будет отправлена в хранилище.
    - Настройка длительного периода удержания резервных копий в базе данных-получателе не требует дополнительной оплаты.



## <a name="next-steps"></a>Дальнейшие действия
Резервные копии базы данных являются важной частью любой стратегии непрерывности бизнес-процессов и аварийного восстановления, так как они защищают данные от случайного повреждения или удаления. Дополнительные сведения о других решениях для базы данных SQL Azure, обеспечивающих непрерывность бизнес-процессов, см. в статье [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure](sql-database-business-continuity.md).


