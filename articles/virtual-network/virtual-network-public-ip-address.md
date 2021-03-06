---
title: "Общедоступные IP-адреса Azure | Документы Майкрософт"
description: "Сведения о создании, изменении и удалении общедоступных IP-адресов."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 430a68ff6a01a53774c1b7843ed774b2ec0d0d36
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="create-change-or-delete-public-ip-addresses"></a>Создание, изменение и удаление общедоступных IP-адресов

Общие сведения об общедоступных IP-адресах, а также сведения об их создании, изменении и удалении. Общедоступный IP-адрес — это ресурс с собственными настраиваемыми параметрами. Назначение общедоступного IP-адреса другим ресурсам Azure связано со следующими преимуществами.
- Возможность входящего подключения к таким интернет-ресурсам, как виртуальные машины Azure, масштабируемые наборы виртуальных машин Azure, VPN-шлюз Azure и Azure Load Balancer с доступом к Интернету.
- Возможность исходящего подключения к сегментам Интернета с адресами, которые не преобразуются (NAT). Например, виртуальная машина может отправлять данные в Интернет, не имея назначенного общедоступного IP-адреса. Ее адресом будет сетевой адрес, преобразуемый Azure. См. дополнительную информацию об [исходящих подключениях](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ресурсов Azure.

В этой статье объясняется, как работать с общедоступными IP-адресами, развернутыми с помощью модели развертывания Azure Resource Manager.

## <a name="before"></a>Перед началом работы

Перед выполнением действий, описанных в любом разделе этой статьи, выполните следующие задачи.

- Просмотрите статью об [ограничениях Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits), чтобы узнать об ограничениях для общедоступных IP-адресов.
- Войдите на портал Azure, в интерфейс командной строки Azure (CLI) или Azure PowerShell с помощью учетной записи Azure. Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной учетной записи](https://azure.microsoft.com/free).
- Если для выполнения задач в этой статье вы используете команды PowerShell, установите и настройте Azure PowerShell, выполнив действия, описанные в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Убедитесь, что у вас установлена самая последняя версия командлетов Azure PowerShell. Для получения справки по командам PowerShell с примерами введите `get-help <command> -full`.
- Если для выполнения задач в этой статье вы используете команды интерфейса командной строки Azure, установите и настройте Azure CLI, выполнив действия, описанные в статье [Установка и настройка интерфейса командной строки Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Убедитесь, что у вас установлена самая последняя версия Azure CLI. Для получения справки по командам интерфейса командной строки введите `az <command> --help`.

За использование общедоступных IP-адресов взимается номинальная плата. Сведения о расценках см. на странице с [ценами на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create"></a> Создание общедоступного IP-адреса

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, которой назначены как минимум разрешения на роль "Участник сети" для подписки. Сведения о назначении ролей и разрешений учетным записям см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *общедоступный IP-адрес*. Когда в результатах поиска появится пункт **Общедоступные IP-адреса**, щелкните его.
3. В открывшейся колонке **Общедоступный IP-адрес** щелкните **+Добавить**.
4. В открывшейся колонке **Создание общедоступного IP-адреса** введите или выберите значения следующих параметров и щелкните **Создать**.

    |Настройка|Обязательный?|Сведения|
    |---|---|---|
    |Имя|Да|Имя должно быть уникальным в пределах выбранной группы ресурсов.|
    |Назначение IP-адресов|Да|**Динамическое** — динамические адреса назначаются, только если общедоступный IP-адрес связан с сетевой картой, подключенной к виртуальной машине, которая запускается в первый раз. Динамические адреса можно изменить, когда сетевая карта подключена к остановленной (освобожденной) виртуальной машине. Если виртуальная машина перезагружается или остановлена (не освобождается), адрес не изменится. **Статическое** — статические адреса назначаются при создании общедоступного IP-адреса. Статические адреса не изменяются, даже если виртуальная машина переводится в остановленное состояние (освобождается). Адрес освобождается только при удалении сетевой карты, Метод назначения можно изменить после создания сетевой карты.|
    |Время ожидания простоя (в минутах)|Нет|Время (в минутах), в течение которого подключение TCP или HTTP остается открытым без привязки к клиентам при отправке запросов для проверки активности.|
    |Метка имени DNS|Нет|Должна быть уникальной в пределах расположения Azure, в котором создается имя (для всех подписок и клиентов). Служба общедоступных имен DNS Azure автоматически регистрирует имя и IP-адрес (вы можете подключаться к ресурсу, используя это имя). Azure добавляет блок *location.cloudapp.azure.com* (где используется выбранное расположение) к имени, которое вы предоставляете для создания полного имени DNS.|
    |Подписки|Да|Ресурс в пределах одной и той же [подписки](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), с которым будет связан общедоступный IP-адрес.|
    |Группа ресурсов|Да|Ресурс в пределах одной или разных [групп ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), с которым будет связан общедоступный IP-адрес.|
    |Расположение|Да|Ресурс в пределах одного и того же [расположения](https://azure.microsoft.com/regions), с которым будет связан общедоступный IP-адрес.|

**Команды**

|Средство|Команда|
|---|---|
|Интерфейс командной строки|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>Изменение параметров или удаление общедоступного IP-адреса

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, которой назначены как минимум разрешения на роль "Участник сети" для подписки. Сведения о назначении ролей и разрешений учетным записям см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *общедоступный IP-адрес*. Когда в результатах поиска появится пункт **Общедоступные IP-адреса**, щелкните его.
3. В открывшейся колонке **Общедоступный IP-адрес** щелкните имя общедоступного IP-адреса для изменения параметров или удаления.
4. В открывшейся колонке "Общедоступный IP-адрес" выполните одно из следующих действий (в зависимости от того, нужно ли удалить этот общедоступный IP-адрес или изменить его).
    - **Удаление** — чтобы удалить общедоступный IP-адрес, в колонке в разделе **Обзор** щелкните **Удалить**. Если адрес связан с IP-конфигурацией, удалить его не получится. В таком случае щелкните **Отменить связь**, чтобы разорвать связь с IP-конфигурацией.
    - **Изменение** — щелкните **Конфигурация**. Измените параметры, используя сведения, описанные в шаге 4 в разделе [Создание общедоступного IP-адреса](#create) этой статьи. Чтобы изменить назначение со статического на динамическое, необходимо сначала разорвать связь между общедоступным IP-адресом и IP-конфигурацией. Затем можно изменить способ назначения на динамическое и щелкнуть **Связать**, чтобы связать IP-адрес с той же или другой IP-конфигурацией либо оставить его несвязанным. Чтобы удалить связь с общедоступным IP-адресом, в разделе **Обзор** щелкните **Отменить связь**.

>[!WARNING]
>Изменив способ назначения со статического на динамический, вы потеряете IP-адрес, который был назначен общедоступному IP-адресу. В Azure общедоступные DNS-серверы поддерживают сопоставление между статическими или динамическими адресами и метками (определенными) имени DNS. При этом динамический IP-адрес можно изменить при запуске виртуальной машины, которая была остановлена (освобождена). Чтобы предотвратить изменение адреса, назначьте статический IP-адрес.

**Команды**

|Средство|Команда|
|---|---|
|Интерфейс командной строки|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) для обновления; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) для удаления|
|PowerShell|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) для обновления; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) для удаления|

## <a name="next-steps"></a>Дальнейшие действия
Назначение общедоступных IP-адресов при создании следующих ресурсов Azure:

- виртуальных машин [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json);
- [Azure Load Balancer с доступом к Интернету](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json);
- [Шлюз приложений Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [подключения типа "сеть — сеть" с помощью VPN-шлюза Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json);
- [масштабируемого набора виртуальных машин Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

