---
title: "Устранение неполадок при выделении ресурсов для виртуальных машин Windows | Документация Майкрософт"
description: "Устранение ошибок выделения ресурсов при создании, перезагрузке или изменении размера виртуальной машины Windows в Azure."
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5313b454a6c6cdc7b9f212302351fafd69c21650
ms.contentlocale: ru-ru
ms.lasthandoff: 04/03/2017


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Устранение ошибок выделения ресурсов при создании, перезагрузке или изменении размера виртуальных машин Windows в Azure
При создании виртуальной машины или изменении ее размера, а также при перезагрузке остановленных (освобожденных) виртуальных машин Microsoft Azure выделяет вычислительные ресурсы для вашей подписки. Иногда во время выполнения этих операций могут возникать ошибки, даже если еще не достигнуты ограничения подписки Azure. В этой статье объясняются причины возникновения некоторых распространенных ошибок выделения, а также представлены возможные способы их устранения. Эта информация также может быть полезна при планировании развертывания служб. Вы также можете [устранить ошибки выделения ресурсов при создании, перезапуске или изменении размера виртуальных машин Linux в Azure](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]


