---
title: "Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт"
description: "Сведения о создании виртуальной машины с ускоренной сетью."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины с ускоренной сетью

В этом руководстве вы узнаете, как создать виртуальную машину Azure с ускоренной сетью. Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор без этой функции, разгружаются и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="Limitations"></a>Ограничения
При использовании этой возможности действуют следующие ограничения:

* **Создание сетевого интерфейса.** Функцию ускорения сети можно включить только в новом сетевом интерфейсе. Ее нельзя включить в имеющемся сетевом интерфейсе.
* **Создание виртуальной машины.** Сетевой интерфейс с включенной функцией ускорения сети можно подключить к виртуальной машине при ее создании. Этого нельзя сделать для имеющейся виртуальной машины.
* **Регионы.** Виртуальные машины Windows с ускоренной сетью доступны в большинстве регионов Azure, в то время как виртуальные машины Linux с этой же функцией — только в двух регионах: юго-центральный регион США и западная часть США 2. Набор поддерживаемых регионов будет расширяться в будущем.
* **Поддерживаемые операционные системы.** Windows: Microsoft Windows Server 2012 R2 Datacenter и Windows Server 2016. Linux: Ubuntu Server 16.04 LTS с ядром версии 4.4.0-77 или более поздней. Вскоре будет добавлена поддержка дополнительных дистрибутивов.
* **Размер виртуальной машины.** Экземпляры общего назначения и оптимизированные для вычислений экземпляры с минимум восемью ядрами. Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Размеры виртуальных машин Linux в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Набор поддерживаемых размеров экземпляров виртуальных машин будет расширяться в будущем.

Сведения об изменениях этих ограничений публикуются на [этой странице](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm"></a>Создание виртуальной машины Windows
Виртуальную машину можно создать на портале Azure или с помощью [Azure PowerShell](#windows-powershell).

### <a name="windows-portal"></a>Портал

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. На портале щелкните **+ Создать** > **Вычисления** > **Windows Server 2016 Datacenter**. 
3. В открывшейся колонке **Windows Server 2016 Datacenter** в разделе **Выбор модели развертывания** выберите *Resource Manager* и нажмите кнопку **Создать**.
4. В открывшейся колонке **Основные данные** введите приведенные ниже значения. В остальных полях можно ввести собственные значения или оставить значения по умолчанию. Затем нажмите кнопку **ОК**.

    |Настройка|Значение|
    |---|---|
    |Имя|MyVm|
    |Группа ресурсов|Выберите **Создать** и введите *MyResourceGroup*.|
    |Расположение|Западный регион США 2|

    Если вы еще не работали с Azure, ознакомьтесь со сведениями о [группах ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [подписках](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) и [расположениях](https://azure.microsoft.com/regions) (регионах).
5. В открывшейся колонке **Выбор размера** в текстовом поле **Minimum cores** (Минимальное число ядер) введите *8*, а затем щелкните **Просмотреть все**.
6. Щелкните **DS4_V2 Standard** и нажмите кнопку **Выбрать**.
7. В открывшейся колонке **Параметры** оставьте все параметры как есть, но в разделе **Ускоренная сеть** выберите **Включено**, а затем нажмите кнопку **ОК**. **Примечание.** Если на предыдущих шагах вы выбрали размер, операционную систему или расположение виртуальной машины, не указанные в разделе [Ограничения](#Limitations) этой статьи, функция **Ускоренная сеть** отображаться не будет.
8. В открывшейся колонке **Сводка** нажмите кнопку **ОК**. После этого в Azure начнется создание виртуальной машины. Этот процесс займет несколько минут.
9. Чтобы установить драйвер ускорения сети для Windows, выполните действия, описанные в разделе [Настройка виртуальных машин Windows](#configure-windows) этой статьи.

### <a name="windows-powershell"></a>PowerShell
1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) Azure PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Общие сведения об Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Запустите сеанс PowerShell. Для этого в главном меню Windows нажмите кнопку "Пуск", введите в строке **powershell** и в результатах поиска выберите **PowerShell**.
3. В окне PowerShell введите команду `login-azurermaccount`, чтобы войти в систему, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. В браузере скопируйте приведенный ниже сценарий.
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. В окне PowerShell щелкните правой кнопкой мыши, чтобы вставить скрипт, и запустите его выполнение. Появится запрос на ввод имени пользователя и пароль. Используйте эти учетные данные, чтобы войти на виртуальную машину при подключении к ней на следующем шаге. Если выполнение скрипта завершилось сбоем и перед этим вы изменили в нем значения, проверьте, используете ли вы размер, расположение и операционную систему виртуальной машины, указанные в разделе [Ограничения](#Limitations) этой статьи.
6. Чтобы установить драйвер ускорения сети для Windows, выполните действия, описанные в разделе [Настройка виртуальных машин Windows](#configure-windows) этой статьи.

### <a name="configure-windows"></a>Настройка виртуальных машин Windows
После создания виртуальной машины в Azure необходимо установить драйвер ускорения сети для Windows. Перед выполнением следующих действий необходимо создать виртуальную машину Windows с ускоренной сетью, используя указания по созданию с помощью [портала](#windows-portal) или [PowerShell](#windows-powershell), описанные в этой статье. 

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *MyVm*. Когда виртуальная машина **MyVm** появится в результатах поиска, щелкните ее.
3. В открывшейся колонке **MyVm** нажмите кнопку **Подключиться** в верхнем левом углу. **Примечание.** Если под кнопкой **Подключиться** отображается элемент **Создание**, это означает, что виртуальная машина по-прежнему создается в Azure. Вы сможете **подключиться** только после того, как под кнопкой **П** исчезнет элемент **Создание**.
4. Разрешите браузеру скачать файл **MyVm.rdp**,  а затем откройте его. 
5. В открывшемся диалоговом окне **Подключение к удаленному рабочему столу** с сообщением о том, что издателя этого удаленного подключения невозможно определить, нажмите кнопку **Подключиться**.
6. В открывшемся поле **Безопасность Windows** щелкните **Больше вариантов**, а затем выберите **Использовать другую учетную запись**. Введите имя пользователя и пароль, указанный на шаге 4, а затем нажмите кнопку **ОК**.
7. В открывшемся диалоговом окне "Подключение к удаленному рабочему столу" с сообщением, что не удается проверить идентификатор удаленного компьютера, нажмите кнопку **Да**.
8. На виртуальной машине MyVm в главном меню Windows нажмите кнопку "Пуск" и выберите **Internet Explorer**.
9. В открывшемся диалоговом окне **Internet Explorer 11** выберите **Использовать рекомендуемые параметры безопасности, конфиденциальности и совместимости** и нажмите кнопку **ОК**.
10. В адресной строке Internet Explorer введите https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84 и нажмите клавишу ВВОД.
11. В открывшемся диалоговом окне **Предупреждение системы безопасности** нажмите кнопку **ОК**.
12. В открывшемся диалоговом окне **Internet Explorer** щелкните **Добавление**, в окне **Надежные сайты** нажмите кнопку **Добавить**, а затем — **Закрыть**. Повторите эту процедуру во всех других открывшихся диалоговых окнах.
13. Чтобы скачать файл, щелкните его.
14. Когда откроется диалоговое окно **License, Terms of Use** (Условия лицензии), нажмите кнопку **Принимаю**.
15. Разрешите Internet Explorer сохранить файл. Для этого в открывшемся в низу экрана диалоговом окне нажмите кнопку **Сохранить**, а затем — **Открыть папку**.
16. Чтобы установить драйвер ускорения сети, дважды щелкните файл. Во время установки примите все параметры по умолчанию и по завершении этого процесса нажмите в мастере кнопку **Да**, чтобы перезапустить виртуальную машину.
17. После перезапуска выполните шаги 9–12 еще раз, чтобы подключиться к виртуальной машине.
18. В главном меню Windows щелкните правой кнопкой мыши кнопку "Пуск" и выберите **Диспетчер устройств**. Разверните узел **Сетевые адаптеры**. Проверьте, есть ли в списке виртуальный адаптер **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**, как показано на следующем рисунке:
   
    ![Диспетчер устройств](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>Создание виртуальной машины Linux
Виртуальную машину можно создать на портале Azure или с помощью [Azure PowerShell](#linux-powershell).

### <a name="linux-portal"></a>Портал
1. Зарегистрируйтесь на получение предварительной версии функции ускорения сети для Linux, выполнив шаги 1–5 в разделе о [создании виртуальной машины Linux с помощью PowerShell](#linux-powershell).  На портале это сделать невозможно.
2. Выполните шаги 1–8 в разделе о [создании виртуальной машины Windows на портале](#windows-portal) этой статьи. На шаге 2 выберите **Ubuntu Server 16.04 LTS**, а не **Windows Server 2016 Datacenter**. В этом руководстве мы будем использовать пароль, а не ключ SSH. Но при развертываниях в рабочей среде можно использовать любой из этих вариантов. Если по завершении шага 7 из раздела о [создании виртуальной машины Windows на портале](#windows-portal) этой статьи функция **Ускорение сети** так и не отобразилась, это может быть связано со следующим:
    - Вы еще не зарегистрированы на получение предварительной версии функции. Проверьте, **зарегистрировались** ли вы, как описано на шаге 4 в разделе о [создании виртуальной машины Linux с помощью Powershell](#linux-powershell) этой статьи. **Примечание.** Если вы зарегистрировались на получение предварительной версии функции ускорения сети для виртуальных машин Windows (сейчас это делать не нужно), это не распространяется автоматически на виртуальные машины Linux. Чтобы использовать эту функцию на виртуальных машинах Linux, зарегистрируйтесь на ее получение.
    - Вы выбрали операционную систему, расположение и размер виртуальной машины, указанные в разделе [Ограничения](#simitations) этой статьи.
3. Чтобы установить драйвер ускорения сети для Linux, выполните действия, описанные в разделе [Настройка виртуальных машин Linux](#configure-linux) этой статьи.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Если вы создаете виртуальные машины Linux с функцией ускорения сети в подписке, а затем пытаетесь создать виртуальную машину Windows с этой же функцией в той же подписке, этот процесс может завершиться сбоем. Во время действия предварительной версии мы советуем создавать виртуальные машины Windows и Linux с функцией ускорения сети в отдельных подписках.
>

1. Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) Azure PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Общие сведения об Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Запустите сеанс PowerShell. Для этого в главном меню Windows нажмите кнопку "Пуск", введите в строке **powershell** и в результатах поиска выберите **PowerShell**.
3. В окне PowerShell введите команду `login-azurermaccount`, чтобы войти в систему, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Зарегистрируйтесь на получение предварительной версии функции ускорения сети Azure, выполнив следующие действия:
    - Введите следующие команды:

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - Отправьте письмо по адресу [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e), указав идентификатор подписки и цель использования. 
    - Чтобы проверить регистрацию на получение предварительной версии функции, выполните следующую команду:
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        Не приступайте к шагу 5, пока в выходных данных команды не отобразится состояние **Зарегистрировано**. Выходные данные должны выглядеть так:
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >Если вы зарегистрировались на получение предварительной версии функции ускорения сети для виртуальных машин Windows (сейчас это делать не нужно), это автоматически не распространяется на виртуальные машины Linux. Чтобы использовать эту функцию на виртуальных машинах Linux, зарегистрируйтесь на ее получение.
      >
5. В браузере скопируйте приведенный ниже сценарий.
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
6. В окне PowerShell щелкните правой кнопкой мыши, чтобы вставить скрипт, и запустите его выполнение. Появится запрос на ввод имени пользователя и пароль. Используйте эти учетные данные, чтобы войти на виртуальную машину при подключении к ней на следующем шаге. При сбое скрипта убедитесь, что:
    - вы зарегистрированы на получение предварительной версии функции, как описано в шаге 4;
    - измененные перед выполнением скрипта значения размера, расположения и операционной системы виртуальной машины указаны в разделе [Ограничения](#Limitations) этой статьи.
7. Чтобы установить драйвер ускорения сети для Linux, выполните действия, описанные в разделе [Настройка виртуальных машин Linux](#configure-linux) этой статьи.

### <a name="configure-linux"></a>Настройка виртуальных машин Linux

После создания виртуальной машины в Azure необходимо установить драйвер ускорения сети для Linux. Перед выполнением следующих действий необходимо создать виртуальную машину Linux с ускоренной сетью, используя указания по созданию с помощью [портала](#linux-portal) или [PowerShell](#linux-powershell), описанные в этой статье. 

1. В браузере откройте [портал Azure](https://portal.azure.com) и войдите, используя [учетную запись](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/offers/ms-azr-0044p).
2. В верхней части портала справа от строки *Поиск ресурсов* щелкните значок **>_**, чтобы запустить Bash Cloud Shell (предварительная версия). После этого в нижней части портала отобразится панель Bash Cloud Shell и через несколько секунд появится командная строка **username@Azure:~$**. Со своего компьютера к виртуальной машине можно подключиться по протоколу SSH, но в рамках этого руководства мы будем использовать Cloud Shell.
3. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *MyVm*. Когда виртуальная машина **MyVm** появится в результатах поиска, щелкните ее.
4. В открывшейся колонке **MyVm** нажмите кнопку **Подключиться** в верхнем левом углу. **Примечание.** Если под кнопкой **Подключиться** отображается элемент **Создание**, это означает, что виртуальная машина по-прежнему создается в Azure. Вы сможете **подключиться** только после того, как под кнопкой **П** исчезнет элемент **Создание**.
5. В Azure откроется окно с запросом на ввод `ssh adminuser@<ipaddress>`. Введите эту команду в Cloud Shell (или скопируйте ее из появившегося на шаге 4 диалогового окна и вставьте в Cloud Shell), а затем нажмите клавишу ВВОД.
6. При появлении запроса на продолжение подключения нажмите кнопку **Да**, а затем нажмите клавишу ВВОД.
7. Введите пароль, использованный при создании виртуальной машины. После успешного входа на виртуальную машину отобразится командная строка adminuser@MyVm:~$. Теперь вы вошли на виртуальную машину через сеанс Cloud Shell. **Примечание.** Срок действия сеанса Cloud Shell истекает после 10 минут бездействия.
8. При появлении запроса введите `uname -r` и подтвердите, что в выходных данных указана версия 4.4.0-77-generic.
9.    Создайте связь между виртуальным сетевым интерфейсом стандартной сети и виртуальным сетевым интерфейсом ускоренной сети, выполнив команды ниже. Сетевой трафик проходит через виртуальный сетевой интерфейс ускоренной сети, а связь гарантирует, что изменение конфигурации не повлияет на этот трафик. 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults` Примечание. При получении ошибки, связанной с *переопределением скрипта bondvf.sh из-за отсутствия в нем тегов LSB*, игнорируйте ее.
    - `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` После этого откроется редактор Nano GNU с файлом.
    - В редакторе закомментируйте строки *auto etho0* и *iface eth0 inet dhcp*, добавив *#* в начале каждой строки. После добавления *#* строки должны выглядеть следующим образом:
        - #<a name="auto-eth0"></a>auto eth0;
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp.
10. Удерживайте нажатыми клавиши **CTRL+X**, введите **Y**, а затем нажмите клавишу **ВВОД**, чтобы сохранить файл.
11. Перезапустите виртуальную машину, выполнив команду `sudo shutdown -r now`.
12. После перезапуска еще раз подключитесь к виртуальной машине, выполнив шаги 5–7.
13.    Выполните команду `ifconfig` и подтвердите, что интерфейс bond0 возобновил работу. **Примечание.** Приложения, использующие функцию ускорения сети, должны взаимодействовать через интерфейс *bond0*, а не *eth0*.  С выходом общедоступной версии функции ускорения сети имя интерфейса может измениться.

