---
title: "Управление журналами потоков для групп безопасности сети с помощью Наблюдателя за сетями (PowerShell) | Документация Майкрософт"
description: "На этой странице объясняется, как управлять журналами потоков для групп безопасности сети с помощью Наблюдателя за сетями и PowerShell"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 9fa2e2e1c09b119bd2a1e149fd2cc080957af296
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---


# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Настройка журналов потоков для групп безопасности сети с помощью PowerShell

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Журналы потоков для групп безопасности сети — это компонент Наблюдателя за сетями, который позволяет просматривать сведения о входящем и исходящем IP-трафике через группу безопасности сети. Эти журналы потоков записываются в формате JSON. В них отображаются входящие и исходящие потоки по каждому правилу, сетевая карта, с которой связан поток, сведения о 5 кортежах потока (IP-адрес источника и места назначения, порт источника и места назначения, протокол), а также сведения о состоянии трафика (разрешен или запрещен).

## <a name="register-insights-provider"></a>Регистрация поставщика Microsoft Insights

Для успешного ведения журналов потоков должен быть зарегистрирован поставщик **Microsoft.Insights**. Если вы не знаете, зарегистрирован ли поставщик **Microsoft.Insights**, выполните следующий сценарий.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Включение журналов потоков для группы безопасности сети

Команда для включения журналов потоков показана в следующем примере.

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG-Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

## <a name="disable-network-security-group-flow-logs"></a>Отключение журналов потоков для группы безопасности сети

Чтобы отключить журналы потоков, используйте следующий пример:

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Скачивание журнала потоков

Место хранения журнала потоков определяется при его создании. Удобное средство для доступа к этим журналам потоков, сохраненным в учетной записи хранения — обозреватель службы хранилища Microsoft Azure, который можно скачать по адресу http://storageexplorer.com/.

При указании учетной записи хранения файлы записи пакетов сохраняются в ней по следующему адресу:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

Сведения о структуре журнала см. в статье [Network Security Group Flow log Overview](network-watcher-nsg-flow-logging-overview.md) (Обзор журнала потоков для группы безопасности сети).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [визуализировать сведения журналов потоков группы безопасности сети с помощью PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

Узнайте, как [визуализировать сведения журналов потоков группы безопасности сети с помощью средств с открытым кодом](network-watcher-visualize-nsg-flow-logs-open-source-tools.md).
