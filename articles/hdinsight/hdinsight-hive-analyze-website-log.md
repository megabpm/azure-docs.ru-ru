---
title: "Использование Hive с Hadoop для анализа журнала веб-сайта | Документация Майкрософт"
description: "Узнайте, как использовать Hive с HDInsight для анализа журналов веб-сайтов. Вы будете использовать файл журнала в качестве входных данных для таблицы HDInsight, а также HiveQL для формирования запроса к данным."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 3322a585de591010925efe8699cc48e32bd8a607
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Использование Hive c HDInsight под управлением Windows для анализа журналов веб-сайтов
Узнайте, как использовать HiveQL с HDInsight для анализа журналов веб-сайта. Анализ журнала веб-сайта можно использовать для разделения аудитории на основе аналогичной деятельности, классификации посетителей сайта по демографическим данным, а также выявления просмотренного ими содержимого, посещенных сайтов и так далее.

> [!IMPORTANT]
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Windows. Для версий ниже HDInsight 3.4 кластер HDInsight доступен только в Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

В данном примере для анализа файлов журнала веб-сайта вы будете использовать кластер HDInsight, чтобы получить представление о дневной частоте посещений веб-сайта из внешних сайтов. Также вы сформируете сводку по ошибкам веб-сайта, с которыми столкнулись пользователи. Вы научитесь:

* подключаться к хранилищу больших двоичных объектов Azure, которое содержит файлы журнала веб-сайта;
* создавать таблицы HIVE для формирования запросов к этим журналам;
* создавать запросы HIVE для анализа данных;
* использовать Microsoft Excel для подключения к HDInsight (с помощью подключения ODBC), чтобы извлекать проанализированные данные.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

## <a name="prerequisites"></a>Предварительные требования
* Вы должны были подготовить кластер Hadoop в Azure HDInsight. Инструкции см. в статье [Создание кластеров Hadoop под управлением Windows в HDInsight][hdinsight-provision].
* У вас должен быть установлен Microsoft Excel 2013 или Microsoft Excel 2010.
* У вас должен быть [Microsoft Hive ODBC драйвер](http://www.microsoft.com/download/details.aspx?id=40886) для импорта данных из Hive в Excel.

## <a name="to-run-the-sample"></a>Запуск образца
1. На начальной панели (если кластер закреплен на ней) [портала Azure](https://portal.azure.com/)щелкните элемент кластера, в котором требуется запустить данный пример.
2. В колонке кластера в разделе **Быстрые ссылки** щелкните **Панель мониторинга кластера**, а затем в колонке **Панель мониторинга кластера** щелкните **Панель мониторинга кластера HDInsight**. Панель мониторинга также можно открыть напрямую с помощью следующего URL-адреса:

         https://<clustername>.azurehdinsight.net

    В ответ на запрос выполните аутентификацию с помощью имени пользователя и пароля администратора, использованных при подготовке данного кластера.
3. На открывшейся веб-странице выберите вкладку **Getting Started Gallery** (Коллекция для начала работы), а затем в категории **Solutions with Sample Data** (Решения с примером данных) щелкните пример **Анализ журналов веб-сайта**.
4. Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец.

## <a name="next-steps"></a>Дальнейшие действия
Попробуйте следующий пример: [Анализ данных датчика с помощью Hive с HDInsight](hdinsight-hive-analyze-sensor-data.md).

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

