---
title: "Примеры Azure PowerShell для базы данных SQL | Документация Майкрософт"
description: "Примеры Azure PowerShell — это сценарии для создания серверов, эластичных пулов, баз данных и брандмауэров базы данных SQL Azure, а также для управления ими."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: c9216c4b5309a91aff56d220307e2757b81012a9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>Примеры Azure PowerShell для базы данных SQL Azure

В следующей таблице содержатся ссылки на примеры сценариев Azure PowerShell для базы данных SQL Azure.

| |  |
|---|---|
|**Создание отдельной базы данных и эластичного пула**||
| [Создание отдельной базы данных и настройка правила брандмауэра](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает отдельную базу данных SQL Azure и настраивает правило брандмауэра уровня сервера. |
| [Создание эластичных пулов и перемещение баз данных в составе пулов](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает эластичные пулы и перемещает базы данных в составе пулов, а также изменяет уровни производительности.|
|**Настройка георепликации и отработка отказа**||
| [Настройка активной георепликации для отдельной базы данных SQL Azure с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Настраивает активную георепликацию для отдельной базы данных SQL Azure и выполняет для нее отработку отказа на вторичную реплику. |
| [Настройка активной георепликации для базы данных SQL Azure в составе пула с помощью PowerShell](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Настраивает активную георепликацию для базы данных SQL Azure в составе эластичного пула и выполняет для нее отработку отказа на вторичную реплику. |
|**Масштабирование отдельных баз данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Отслеживает метрики производительности базы данных SQL Azure, масштабирует ее до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности. |
| [Масштабирование эластичного пула](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Отслеживает метрики производительности эластичного пула, масштабирует его до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности.  |
| **Аудит и обнаружение угроз** |
| [Настройка аудита и обнаружения угроз](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Настраивает политики аудита и обнаружения угроз для базы данных SQL Azure. |
| **Восстановление, копирование и импорт базы данных**||
| [Восстановление базы данных](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Восстанавливает базу данных SQL Azure из геоизбыточной резервной копии и восстанавливает последнюю резервную копию удаленной базы данных SQL Azure. |
| [Копирование базы данных на новый сервер](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает копию существующей базы данных SQL Azure на новом сервере SQL Azure. |
| [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Импортирует базу данных из BACPAC-файла на сервер SQL Azure. |
|||

