---
title: "Создание масштабируемых наборов виртуальных машин для Windows в Azure | Документация Майкрософт"
description: "Создание и развертывание высокодоступного приложения на виртуальных машинах Windows с помощью масштабируемого набора виртуальных машин."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 05/02/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 8a5f6e8bf01c8bc38f3fd327acd0ddc8f9cdd7de
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Создание масштабируемого набора виртуальных машин и развертывание высокодоступного приложения в Windows
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Можно вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. В рамках этого руководства вы развернете масштабируемый набор виртуальных машин в Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов для определения масштабируемого сайта IIS
> * Создание балансировщика нагрузки для масштабируемого набора
> * Создание масштабируемого набора виртуальных машин
> * Увеличение или уменьшение количества экземпляров в масштабируемом наборе
> * Создание правил автомасштабирования

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="scale-set-overview"></a>Обзор масштабируемого набора
Масштабируемые наборы используют те же принципы, которые вы изучили в предыдущем руководстве по [созданию высокодоступных виртуальных машин](tutorial-availability-sets.md). Виртуальные машины в масштабируемом наборе распределяются по доменам сбоя и доменам обновления, как и виртуальные машины в группе доступности.

Виртуальные машины создаются в масштабируемом наборе по мере необходимости. Можно определить правила автомасштабирования, чтобы управлять добавлением и удалением виртуальных машин в масштабируемом наборе. Эти правила могут активироваться на основе метрик, таких как использование ЦП, использование памяти или сетевой трафик.

При использовании образа платформы Azure масштабируемые наборы поддерживают до 1000 виртуальных машин. Для рабочих нагрузок, требующих установки значительного числа компонентов или сложной настройки виртуальных машин, вы можете [создать образ виртуальной машины](tutorial-custom-images.md). При использовании пользовательского образа в масштабируемом наборе можно создать до 100 виртуальных машин.


## <a name="create-an-app-to-scale"></a>Создание приложения для масштабирования
Прежде чем создать масштабируемый набор, выполните команду [az group create](/powershell/module/azurerm.resources/new-azurermresourcegroup) для создания группы ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroupAutomate* в расположении *EastUS*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupScaleSet -Location EastUS
```

В предыдущем руководстве вы узнали, как [автоматизировать настройку виртуальных машин](tutorial-automate-vm-deployment.md) с помощью расширения пользовательских сценариев. Создайте конфигурацию масштабируемого набора, а затем примените расширение пользовательских сценариев, чтобы установить и настроить IIS.

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location EastUS `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
```

## <a name="create-scale-load-balancer"></a>Создание подсистемы балансировки нагрузки для масштабируемого набора
Azure Load Balancer представляет собой балансировщик нагрузки уровня 4 (TCP, UDP), который обеспечивает высокий уровень доступности, распределяя входящий трафик между работоспособными виртуальными машинами. Проба работоспособности позволяет отслеживать данный порт на каждой виртуальной машине и передавать трафик только в рабочую виртуальную машину. Дополнительные сведения см. в следующем руководстве, [Балансировка нагрузки виртуальных машин Windows в Azure для создания высокодоступного приложения](tutorial-load-balancer.md).

Создайте подсистему балансировки нагрузки с общедоступным IP-адресом, которая распределяет веб-трафик через порт 80.

```powershell
# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupScaleSet `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Теперь создайте масштабируемый набор виртуальных машин с помощью командлета [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm). Приведенный ниже пример создает масштабируемый набор *myScaleSet*.

```powershell
# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword P@ssword! `
  -ComputerNamePrefix myVM

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myScaleSet `
  -VirtualMachineScaleSet $vmssConfig     
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="test-your-app"></a>Тестирование приложения
Чтобы изучить работу веб-сайта IIS, получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример получает IP-адрес элемента *myPublicIP*, созданного ранее вместе с масштабируемым набором.

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupScaleSet -Name myPublicIP | select IpAddress
```

Введите общедоступный IP-адрес в веб-браузер. Отобразится приложение, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик.

![Выполнение сайта IIS](./media/tutorial-create-vmss/running-iis-site.png)

Чтобы познакомиться с работой масштабируемого набора, принудительно обновите окно веб-браузера, чтобы увидеть, как балансировщик нагрузки распределяет трафик между всеми виртуальными машинами, где выполняется приложение.


## <a name="management-tasks"></a>Задачи управления
На протяжении жизненного цикла масштабируемого набора может возникнуть необходимость выполнить одну или несколько задач управления. Кроме того, можно создавать сценарии для автоматизации различных задач жизненного цикла. Azure PowerShell позволяет быстро выполнять эти задачи. Ниже приведено несколько распространенных задач.

### <a name="view-vms-in-a-scale-set"></a>Просмотр виртуальных машин в масштабируемом наборе
Чтобы просмотреть список виртуальных машин, запущенных в масштабируемом наборе, выполните командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) следующим образом.

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Loop through the instanaces in your scale set
for ($i=0; $i -le ($scaleset.Sku.Capacity - 1); $i++) {
    Get-AzureRmVmssVM -ResourceGroupName myResourceGroupScaleSet `
      -VMScaleSetName myScaleSet `
      -InstanceId $i
}
```


### <a name="increase-or-decrease-vm-instances"></a>Увеличение или уменьшение числа экземпляров виртуальной машины
Чтобы просмотреть количество экземпляров, присутствующих в масштабируемом наборе, выполните командлет [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) и отправьте запрос для *sku.capacity*.

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -VMScaleSetName myScaleSet | `
    Select -ExpandProperty Sku
```

После этого можно вручную увеличить или уменьшить число виртуальных машин в масштабируемом наборе, выполнив командлет [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Приведенный ниже пример задает число виртуальных машин в масштабируемом наборе, равное *5*.

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -Name myScaleSet `
    -VirtualMachineScaleSet $scaleset
```

Обновление числа экземпляров в масштабируемом наборе занимает несколько минут.


### <a name="configure-autoscale-rules"></a>Настройка правил автомасштабирования
Вместо установки числа экземпляров в масштабируемом наборе вручную можно определить правила автомасштабирования. С помощью правил среда отслеживает экземпляры в масштабируемом наборе и реагирует соответствующим образом на основе определенных метрик и пороговых значений. Приведенный ниже пример увеличивает число экземпляров на один, когда средняя загрузка ЦП превышает 60 % в течение 5 минут. Если затем средняя загрузка ЦП не превышает 30 % в течение 5 минут, то число экземпляров уменьшается на один.

```powershell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription).SubscriptionId
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "West US"

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5 minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5 minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="next-steps"></a>Дальнейшие действия
В рамках этого руководства вы создали набор масштабирования виртуальных машин. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов для определения масштабируемого сайта IIS
> * Создание балансировщика нагрузки для масштабируемого набора
> * Создание масштабируемого набора виртуальных машин
> * Увеличение или уменьшение количества экземпляров в масштабируемом наборе
> * Создание правил автомасштабирования

Перейдите к следующему руководству, чтобы узнать о принципах балансировки нагрузки для виртуальных машин.

> [!div class="nextstepaction"]
> [Балансировка нагрузки между виртуальными машинами](tutorial-load-balancer.md)

