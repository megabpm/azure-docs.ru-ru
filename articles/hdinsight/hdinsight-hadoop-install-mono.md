---
title: "Установка или обновление Mono в HDInsight | Документация Майкрософт"
description: "Сведения об использовании конкретной версии Mono с кластером HDInsight. Mono используется для запуска приложений .NET в кластерах HDInsight под управлением Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: dc303b5386d48cd7daad1630a35c8b8366a9c19f
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---

# <a name="install-or-update-mono-on-hdinsight"></a>Установка или обновление Mono в HDInsight

Сведения об установке конкретной версии [Mono](https://www.mono-project.com) в HDInsight версии 3.4 или выше.

Mono устанавливается на HDInsight версии 3.4 и выше и используется для запуска приложений .NET. Сведения о версии Mono, которая входит в состав каждой версии HDInsight, см. в статье [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md). Чтобы установить разные версии в кластере, используйте действия скрипта, рассматриваемые в этом документе. 

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующий параметр.

* __Номер версии Mono.__ Версия Mono, которую необходимо установить. Версия должна быть доступна по адресу [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

С помощью скрипта можно установить следующие пакеты Mono:

* __mono-complete__;

* __ca-certificates-mono__.

## <a name="the-script"></a>Сценарий

__Расположение скрипта:__ [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Требования__

* Скрипт должен применяться к __головным узлам__ и __рабочим узлам__.

## <a name="to-use-the-script"></a>Использование скрипта

Сведения об использовании этого скрипта с HDInsight см. в документе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Вы можете использовать скрипт с помощью портала Azure, Azure PowerShell или интерфейса командной строки Azure.

При выполнении документа со сведениями о действиях скрипта используйте следующий универсальный код ресурса (URI):

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

> [!NOTE]
> Чтобы настроить HDInsight с помощью этого скрипта, пометьте скрипт как __сохраненный__. Этот параметр позволяет HDInsight применять скрипт к рабочим узлам, добавленным с помощью операций масштабирования.


## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как обновить или установить конкретную версию Mono в HDInsight. Дополнительные сведения об использовании приложений .NET в HDInsight см. в следующих статьях:

* [Использование языка C# для потоковой передачи MapReduce в Hadoop в HDInsight](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md);
* [Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md);
* [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md);
* [Перенос решений .NET из HDInsight под управлением Windows в HDInsight под управлением Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

Дополнительные сведения об использовании действий скрипта см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
