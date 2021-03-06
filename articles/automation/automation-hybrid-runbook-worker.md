---
title: "Гибридные рабочие роли Runbook в службе автоматизации Azure | Документация Майкрософт"
description: "В этой статье содержатся сведения об установке и использовании гибридного компонента Runbook Worker, который является элементом службы автоматизации Azure, позволяющей запускать модули Runbook на компьютерах в локальном центре обработки данных."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a63778c300a3d215a2a0025824f6363e1d9e9675
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Автоматизация ресурсов в центре обработки данных с помощью гибридной рабочей роли Runbook
Модули Runbook в службе автоматизации Azure не могут получить доступ к ресурсам локального центра обработки данных, поскольку они выполняются в облаке Azure.  Функция гибридной рабочей роли Runbook службы автоматизации Azure позволяет запускать модули runbook на компьютерах, расположенных в центре обработки данных, для управления локальными ресурсами. Модули Runbook сохраняются и управляются в службе автоматизации Azure, а затем передаются на один или несколько локальных компьютеров.  

Эта функция проиллюстрирована на рисунке ниже.<br>  

![Обзор гибридного компонента Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Вы можете назначить один или несколько компьютеров в вашем центре обработки данных в качестве гибридного компонента Runbook Worker и запускать модули Runbook при помощи службы автоматизации Azure.  Каждый компонент Worker требует наличия агента управления Майкрософт с подключением к Microsoft Operations Management Suite и средой Runbook службы автоматизации Azure.  Operations Management Suite используется только для установки и технического обслуживания агента управления, а также для отслеживания работы функций компонента Worker.  Предоставление элементов Runbook и инструкций по их запуску выполняется службой автоматизации Azure.

Для поддержки гибридных компонентов Runbook Worker отсутствуют требования к брандмауэру для входящих подключений. Агент на локальном компьютере инициирует весь обмен данными со службой автоматизации Azure в облаке. При запуске модуля Runbook служба автоматизации Azure создает инструкцию, которая извлекается агентом. После этого агент извлекает модуль Runbook и все сопутствующие параметры, применяемые перед запуском модуля.  Кроме того, он извлекает из службы автоматизации Azure все [ресурсы](http://msdn.microsoft.com/library/dn939988.aspx), которые используются в модуле runbook.

> [!NOTE]
> Чтобы управлять конфигурацией серверов, поддерживающих гибридную рабочую роль Runbook с настройкой требуемого состояния (DSC), добавьте такие серверы в качестве узлов DSC.  Дополнительные сведения о развертывании этих узлов для управления с помощью DSC см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).           
><br>
>Если вы включите [решение по управлению обновлениями](../operations-management-suite/oms-solution-update-management.md), любой подключенный к рабочей области OMS компьютер Windows будет автоматически настроен в качестве гибридной рабочей роли Runbook для поддержки модулей runbook, которые входят в это решение.  Но он не регистрируется в группах гибридных рабочих ролей, которые уже определены в вашей учетной записи службы автоматизации.  Его можно добавить в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации, чтобы обеспечить поддержку модулей Runbook службы автоматизации при условии, что вы используете одну и ту же учетную запись для решения и для членства в группе гибридных рабочих ролей Runbook.  Эта функция добавлена в версии 7.2.12024.0 гибридной рабочей роли Runbook.  

## <a name="hybrid-runbook-worker-groups"></a>Группы гибридных компонентов Runbook Worker
Каждый гибридный компонент Runbook Worker является членом группы гибридных компонентов Runbook Worker, которая указывается при установке агента.  Группа может включать одного агента, но в нее можно установить несколько агентов для обеспечения высокого уровня доступности.

Когда вы запускаете модуль runbook в гибридной рабочей роли Runbook, вы выбираете группу для его выполнения.  Члены группы самостоятельно определят, какая из служб рабочей роли будет обслуживать этот запрос.  Указание конкретного компонента Worker для выполнения конкретной задачи не предусмотрено.

## <a name="hybrid-runbook-worker-requirements"></a>Требования к гибридному компоненту Runbook
Для выполнения заданий гибридного компонента Runbook необходимо назначить хотя бы один локальный компьютер.  На компьютере должны быть установлены:

* Windows Server 2012 или более поздней версии;
* Windows PowerShell, начиная с версии 4.0.  Для повышения надежности работы мы советуем установить на компьютерах Windows PowerShell версии 5.0. Новую версию можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=50395).
* Не менее двух ядер процессора и 4 ГБ ОЗУ.

При работе с гибридными компонентами Worker учтите следующие рекомендации:

* Назначьте несколько гибридных компонентов Worker в каждой группе, чтобы обеспечить высокую доступность.  
* Гибридные компоненты Worker могут сосуществовать с серверами Runbook Service Management Automation и System Center Orchestrator.
* По возможности следует использовать компьютер, расположенный в том же регионе, что и ваша учетная запись службы автоматизации (или рядом с ним), так как данные выполненного задания отправляются в службу автоматизации Azure.

### <a name="configure-proxy-and-firewall-settings"></a>Настройка прокси-сервера и брандмауэра
Чтобы локальная гибридная рабочая роль Runbook могла подключаться и регистрироваться в службе Microsoft Operations Management Suite (OMS), ей нужен доступ к описанным ниже портам и URL-адресам.  Этот список является дополнением к [списку ресурсов и портов](../log-analytics/log-analytics-windows-agents.md), необходимых для подключения Microsoft Monitoring Agent к OMS. При использовании прокси-сервера для обмена данными между агентом и службой OMS необходимо убедиться, что соответствующие ресурсы доступны. Если доступ к Интернету ограничивается брандмауэром, вам нужно изменить его настройки, чтобы разрешить доступ к OMS.

Приведенные ниже сведения — это список портов и URL-адресов для обмена данными между гибридной рабочей ролью Runbook и службой автоматизации.

* Порт: только исходящий трафик Интернета через TCP-порт 443.
* Глобальный URL-адрес: *.azure-automation.net.

Если ваша учетная запись службы автоматизации настроена для использования в определенном регионе и нужно настроить обмен данными только с центром обработки данных в этом регионе, выберите соответствующие записи DNS из следующей таблицы.

| **Регион** | **Запись DNS** |
| --- | --- |
| Южно-центральный регион США |scus-jobruntimedata-prod-su1.azure-automation.net |
| Восток США 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Западно-центральная часть США | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Западная Европа |we-jobruntimedata-prod-su1.azure-automation.net |
| Северная Европа |ne-jobruntimedata-prod-su1.azure-automation.net |
| Центральная Канада |cc-jobruntimedata-prod-su1.azure-automation.net |
| Юго-Восточная Азия |sea-jobruntimedata-prod-su1.azure-automation.net |
| Центральная Индия |cid-jobruntimedata-prod-su1.azure-automation.net |
| Восточная часть Японии |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Юго-Восточная Австралия |ase-jobruntimedata-prod-su1.azure-automation.net |
| Южная часть Великобритании | uks-jobruntimedata-prod-su1.azure-automation.net |
| Правительство штата Вирджиния | usge-jobruntimedata-prod-su1.azure-automation.us |

Для списка IP-адресов вместо имен скачайте XML-файл [IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) из Центра загрузки Майкрософт и ознакомьтесь с ним.

> [!NOTE]
> Этот файл содержит диапазоны IP-адресов (включая диапазоны вычисления, хранения и SQL), используемые в центрах обработки данных Microsoft Azure. Обновленный файл публикуется еженедельно и отражает развернутые в настоящее время диапазоны и все предстоящие изменения IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю. Скачивайте новый XML-файл каждую неделю и вносите соответствующие изменения на своем сайте, чтобы правильно определять службы, выполняемые в Azure. Пользователи Express Route могут заметить, что этот файл используется для того, чтобы обновлять объявление BGP пространства Azure в первую неделю каждого месяца.
>

## <a name="installing-hybrid-runbook-worker"></a>Установка гибридного компонента Runbook Worker

Ниже описаны два метода установки и настройки гибридной рабочей роли Runbook.  Мы рекомендуем использовать модуль runbook службы автоматизации для полностью автоматической настройки компьютера Windows.  Второй метод заключается в пошаговом выполнении инструкций по ручной установке и настройке роли.  

### <a name="automated-deployment"></a>Автоматизированное развертывание

Выполните следующие действия для автоматизации установки и настройки гибридной рабочей роли.  

1. Скачайте сценарий *New-OnPremiseHybridWorker.ps1* из [коллекции PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) непосредственно на компьютер, на котором выполняется гибридная рабочая роль Runbook, или на другой компьютер в вашей среде, после чего скопируйте этот сценарий в рабочую роль.  

    Для выполнения сценария *New-OnPremiseHybridWorker.ps1* необходимо задать следующие параметры.

  * *AutomationAccountName* (обязательный) — имя учетной записи службы автоматизации;  
  * *ResourceGroupName* (обязательный) — имя группы ресурсов, связанной с вашей учетной записью автоматизации.  
  * *HybridGroupName* (обязательный) — имя группы гибридных рабочих ролей Runbook, которую вы указываете в качестве целевой для модулей runbook, поддерживающих этот сценарий.
  *  *SubscriptionID* (обязательный) — идентификатор подписки Azure, которая используется для учетной записи автоматизации.
  *  *WorkspaceName* (необязательный) — имя рабочей области OMS.  Если у вас нет рабочей области OMS, скрипт создаст и настроит ее.  

     > [!NOTE]
     > В настоящее время интеграция с OMS поддерживается только для следующих регионов службы автоматизации: **Юго-восточная Австралия**, **Восточная часть США 2**, **Юго-Восточная Азия** и **Западная Европа**.  Если ваша учетная запись службы автоматизации не находится в одном из этих регионов, скрипт все равно создаст рабочую область OMS, но предупредит, что связывание невозможно.
     >
2. На своем компьютере запустите **Windows PowerShell** на **начальном** экране в режиме администратора.  
3. В оболочке командной строки PowerShell перейдите к папке, которая содержит скачанный сценарий, и выполните его, изменив значения параметров *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* и *-WorkspaceName*.

     > [!NOTE]
     > После выполнения скрипта вы увидите запрос на аутентификацию в Azure.  Для входа **необходимо** использовать учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.  
     >  

        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Также вы получите предложение подтвердить установку пакета **NuGet** и указать учетные данные Azure для аутентификации.<br><br> ![Выполнение сценария New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. После завершения сценария в колонке "Группы гибридных рабочих ролей" отобразится новая группа и число элементов в ней с учетом только что добавленных.  Вы можете выбрать группу из списка на вкладке **Группы гибридных рабочих ролей** и щелкнуть колонку **Гибридные рабочие роли**.  В колонке **Гибридные рабочие роли** отображается список элементов группы.  

### <a name="manual-deployment"></a>Развертывание вручную
Выполните первые два шага для среды автоматизации, а затем повторите остальные шаги для каждого компьютера с компонентом Worker.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Создайте рабочую область Operations Management Suite
Если у вас еще нет рабочей области Operations Management Suite, [создайте](../log-analytics/log-analytics-manage-access.md) ее. Если у вас уже есть рабочая область, вы можете использовать ее.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2) Добавьте решение автоматизации в рабочую область Operations Management Suite
Решения расширяют функциональные возможности Operations Management Suite.  Решение автоматизации расширяет функциональные возможности службы автоматизации Azure, включая поддержку гибридных компонентов Runbook Worker.  Когда вы добавляете решение в рабочую область, она автоматически загружает компоненты рабочей роли на компьютер агента (вы установите его на следующем этапе).

Выполните инструкции в статье [Добавление решения с использованием коллекции решений](../log-analytics/log-analytics-add-solutions.md) , чтобы добавить решение **службы автоматизации** в рабочую область Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Установка Microsoft Monitoring Agent
Microsoft Monitoring Agent подключает компьютеры к Operations Management Suite.  Когда агент устанавливается на локальный компьютер и подключается к рабочей области, он автоматически скачивает компоненты, необходимые для гибридной рабочей роли Runbook.

Установите агент на локальный компьютер, следуя инструкциям в статье [Подключение компьютеров Windows к Log Analytics](../log-analytics/log-analytics-windows-agents.md).  Выполните эту процедуру на нескольких компьютерах, чтобы добавить в среду несколько компонентов Worker.

Агент, успешно подключенный к Operations Management Suite, появится на вкладке **Подключенные источники** в области **Параметры** Operations Management Suite.  Если агент скачал решение службы автоматизации правильно, в каталоге C:\Program Files\Microsoft Monitoring Agent\Agent появится папка **AzureAutomationFiles**.  Чтобы проверить версию гибридной рабочей роли Runbook, откройте папку C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ и найдите вложенную папку \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Установите среду модулей Runbook и выполните подключение к службе автоматизации Azure
При добавлении агента в Operational Management Suite решение службы автоматизации устанавливает модуль PowerShell **HybridRegistration**, который содержит командлет **Add-HybridRunbookWorker**.  Этот командлет используется для установки среды модулей Runbook на компьютере и ее регистрации в службе автоматизации Azure.

Откройте сеанс PowerShell с правами администратора и импортируйте модуль, выполнив указанные ниже команды.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

После этого запустите командлет **Add-HybridRunbookWorker** , используя следующий синтаксис:

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Данные, необходимые для этого командлета, можно найти в колонке **Управление ключами** на портале Azure.  Откройте эту колонку, выбрав параметр **Ключи** в колонке **Параметры** для учетной записи службы автоматизации.

![Обзор гибридного компонента Runbook Worker](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Имя** — это имя группы гибридных компонентов Runbook Worker. Если эта группа уже существует в учетной записи автоматизации, то к ней добавляется текущий компьютер.  Если она еще не существует, то выполняется ее добавление.
* **Конечная точка** — это значение поля **URL-адрес** в колонке **Управление ключами**.
* **Токен** — это **первичный ключ доступа** в колонке **Управление ключами**.  

Для получения подробных сведений об установке используйте параметр **-Verbose** с командлетом **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Установка модулей PowerShell
Модули Runbook могут использовать любые из действий и командлетов, которые определены в модулях, установленных в вашей среде службы автоматизации Azure.  Эти модули не разворачиваются на локальных компьютерах автоматически, поэтому их необходимо устанавливать вручную.  Исключением является модуль Azure, который устанавливается по умолчанию и предоставляет доступ к командлетам для всех служб и действий службы автоматизации Azure.

Так как главной целью функции гибридной рабочей роли Runbook является управление локальными ресурсами, вам могут потребоваться модули, которые поддерживают эти ресурсы.  Сведения об установке модулей Windows PowerShell можно найти в разделе [Установка модулей](http://msdn.microsoft.com/library/dd878350.aspx) .  Устанавливаемые модули должны находиться в расположении, указанном в переменной среды PSModulePath, чтобы гибридная рабочая роль автоматически импортировала их.  Дополнительные сведения см. в статье [об изменении пути установки PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="removing-hybrid-runbook-worker"></a>Удаление гибридного компонента Runbook Worker
Можно удалить одну или несколько гибридных рабочих ролей Runbook из группы либо удалить группу, в зависимости ваших целей.  Чтобы удалить гибридную рабочую роль Runbook на локальном компьютере, выполните следующие действия.

1. На портале Azure перейдите к учетной записи службы автоматизации.  
2. В колонке **Параметры** щелкните **Ключи** и запишите значения полей **URL-адрес** и **Первичный ключ доступа**.  Эти сведения потребуются для выполнения следующего шага.
3. Откройте сеанс PowerShell в режиме администратора и выполните команду `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Получить подробный журнал процедуры удаления можно с помощью параметра **-Verbose** .

> [!NOTE]
> Агент мониторинга Майкрософт не удаляется с компьютера, удаляются только функциональные возможности и настройки гибридной рабочей роли Runbook.  

## <a name="remove-hybrid-worker-groups"></a>Удаление групп гибридных рабочих ролей
Чтобы удалить группу, необходимо сначала удалить гибридную рабочую роль Runbook с каждого компьютера, который является участником этой группы, с помощью приведенной выше процедуры, а затем выполнить приведенные ниже инструкции по удалению группы.  

1. На портале Azure откройте учетную запись службы автоматизации.
2. Выберите элемент **Группы гибридных рабочих ролей** и в колонке **Группы гибридных рабочих ролей** выберите группу, которую требуется удалить.  После выбора конкретной группы отобразится колонка свойств **гибридной рабочей роли**.<br> ![Колонка группы гибридных рабочих ролей Runbook](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. В колонке свойств выбранной группы щелкните **Удалить**.  Появится запрос на подтверждение этого действия. Выберите **Да**, если вы уверены, что хотите продолжить.<br> ![Диалоговое окно подтверждения удаления группы](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Этот процесс может занять несколько секунд. Ход его выполнения можно просмотреть в разделе **Уведомления** в меню.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Запуск модулей Runbook на гибридном компоненте Runbook Worker
[Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md) описываются различные методы запуска модуля Runbook.  Гибридный компонент Runbook Worker добавляет параметр **RunOn** , при помощи которого можно указать имя группы гибридных компонентов Runbook Worker.  При указании группы модуль Runbook извлекается и запускается компонентами Worker в этой группе.  Если этот параметр не указан, то он будет запущен в службе автоматизации Azure в обычном режиме.

При запуске модуля Runbook на портале Azure отображается запрос **Выполнить в**, где вы можете выбрать в качестве значения варианты **Azure** или **Гибридная рабочая роль**.  При выборе значения **Гибридный компонент Worker**можно выбрать группу из раскрывающегося списка.

Используйте параметр **RunOn**.  Вы можете использовать следующую команду, чтобы с помощью Windows PowerShell запустить модуль runbook с названием Test-Runbook в группе гибридных рабочих ролей Runbook с именем MyHybridGroup.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> Параметр **RunOn** был добавлен в командлет **Start-AzureAutomationRunbook** в Microsoft Azure PowerShell версии 0.9.1.  Если у вас установлена более ранняя версия, следует [загрузить последнюю версию](https://azure.microsoft.com/downloads/) .  Эту версию достаточно установить только на той рабочей станции, на которой вы будете запускать модуль runbook с помощью Windows PowerShell.  Вам не нужно устанавливать его на компьютер с компонентом Worker, если вы не собираетесь запускать модули Runbooks с этого компьютера.  В настоящий момент вы не можете запускать модуль Runbook в гибридном компоненте Runbook Worker из другого модуля Runbook, поскольку для этого потребуется установить последнюю версию Azure Powershell в учетной записи службы автоматизации.  При изменении версии она автоматически обновляется в службе автоматизации Azure и принудительно устанавливается в рабочих ролях.
>
>

## <a name="runbook-permissions"></a>Разрешения для модулей Runbook
Так как модули runbook, запущенные в гибридной рабочей роли Runbook, будут обращаться к ресурсам за пределами Azure, они не смогут использовать стандартный метод аутентификации в Azure.  Модуль Runbook может предоставить собственную проверку подлинности для локальных ресурсов. Также можно указать учетную запись запуска от имени, чтобы предоставить контекст пользователя для всех модулей.

### <a name="runbook-authentication"></a>Проверка подлинности модуля Runbook
По умолчанию модули Runbook выполняются в контексте локальной системной учетной записи на локальном компьютере. Поэтому они должны предоставлять собственные средства аутентификации при доступе к требуемым ресурсам.  

В модуле Runbook можно использовать ресурсы [учетных данных](http://msdn.microsoft.com/library/dn940015.aspx) и [сертификата](http://msdn.microsoft.com/library/dn940013.aspx) с командлетами, которые позволяют указывать такие данные для аутентификации доступа к разным ресурсам.  В следующем примере показана часть модуля Runbook, предназначенная для перезапуска компьютера.  Он извлекает учетные данные из набора учетных данных, а также имя компьютера из набора переменных, после чего использует эти значения в сочетании с командлетом Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Вы можете также использовать [InlineScript](automation-powershell-workflow.md#inlinescript), что позволяет запускать блоки кода на другом компьютере с учетными данными, указанными в [общем параметре PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Учетная запись запуска от имени
Кроме варианта, когда модули Runbook выполняют собственную проверку подлинности на локальных ресурсах, вы можете указать для группы гибридных рабочих ролей учетную запись **Запуск от имени** .  Здесь вы можете указать [ресурс учетных данных](automation-credentials.md), который будет использоваться для доступа к локальным ресурсам всеми модулями runbook, которые выполняются гибридными рабочими ролями Runbook, входящими в эту группу.  

Имя пользователя для учетных данных должно быть представлено в одном из следующих форматов:

* домен\имя пользователя;
* username@domain
* имя пользователя (для локальных учетных записей на локальном компьютере).

Указать учетную запись запуска от имени для гибридной рабочей роли можно так.

1. Создайте [ресурс учетных данных](automation-credentials.md) с доступом к локальным ресурсам.
2. На портале Azure откройте учетную запись службы автоматизации.
3. Щелкните плитку **Группы гибридных рабочих ролей** и выберите группу.
4. Щелкните **Все параметры**, а затем — **Настройки группы гибридных рабочих ролей**.
5. Измените для параметра **Запуск от имени** значение **По умолчанию** на **Пользовательский**.
6. Выберите учетные данные и нажмите кнопку **Сохранить**.

### <a name="automation-run-as-account"></a>Учетная запись запуска от имени службы автоматизации
В ходе автоматизированного процесса развертывания ресурсов в Azure вам может потребоваться создать в локальных системах запрос на поддержку задачи или набора действий, входящих в последовательность развертывания.  Чтобы использовать аутентификацию в Azure с использованием учетной записи запуска от имени, необходимо установить сертификат учетной записи запуска от имени.  

Модуль runbook PowerShell с именем *Export-RunAsCertificateToHybridWorker* экспортирует сертификат запуска от имени из учетной записи службы автоматизации Azure, а затем скачивает его и импортирует в хранилище сертификатов локального компьютера, относящемся к гибридной рабочей роли, подключенной к этой учетной записи.  После этого модуль проверяет, может ли рабочая роль успешно выполнить аутентификацию в Azure с использованием этой учетной записи запуска от имени.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT
    .TAGS Azure Automation
    .LICENSEURI
    .PROJECTURI
    .ICONURI
    .EXTERNALMODULEDEPENDENCIES
    .REQUIREDSCRIPTS
    .EXTERNALSCRIPTDEPENDENCIES
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])]

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

    Write-Output ("Importing certificate into local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Select-AzureRmSubscription -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Сохраните модуль runbook *Export-RunAsCertificateToHybridWorker* на компьютер с расширением `.ps1`.  Импортируйте модуль runbook в учетную запись автоматизации и замените значение переменной `$Password` своим паролем.  Опубликуйте и запустите модуль runbook для группы гибридных рабочих ролей, в которой для запуска и аутентификации модулей runbook применяется учетная запись запуска от имени.  Поток заданий сообщает о попытке импортировать сертификат в хранилище локального компьютера, а затем передает несколько строк с сообщениями в зависимости от числа учетных записей автоматизации, которые определены в подписке, и успешности аутентификации.  

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Создание модулей Runbook для гибридного компонента Runbook Worker
В структуре модулей Runbook, которые работают в службе автоматизации Azure, отсутствует разница между ними и теми модулями, которые работают в гибридном компоненте Runbook Worker. Модули runbook, которые используются в каждом из этих вариантов, скорее всего, будут значительно различаться. Причина — модули runbook для гибридной рабочей роли Runbook обычно управляют локальными ресурсами в центре обработки данных, а модули runbook в службе автоматизации Azure управляют ресурсами в облаке Azure.

Вы можете отредактировать модуль Runbook для гибридного компонента Runbook Worker в службе автоматизации Azure, однако вы можете столкнуться с трудностями при попытке тестирования модуля Runbook в редакторе.  Модули PowerShell, которые осуществляют доступ к локальным ресурсам, не могут устанавливаться в среде службы автоматизации Azure; если это будет сделано, успешное тестирование станет невозможным.  Если вы все же установите необходимые модули, то модуль Runbook будет запущен, однако он не сможет получить доступ к локальным ресурсам в степени, необходимой для завершения тестирования.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Устранение неполадок с модулями Runbook в гибридном компоненте Runbook Worker
[Выходные данные Runbook и сообщения](automation-runbook-output-and-messages.md) отправляются в службу автоматизации Azure из гибридных рабочих ролей точно так же, как задания Runbook, которые выполняются в облаке.  Потоки Verbose и Progress можно активировать точно так же, как и для других модулей Runbook.  

Журналы сохраняются локально в каждом гибридном компоненте Worker по адресу C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.

Если ваши модули runbook выполняются с ошибками, а сводка о задании отображает состояние **Приостановлено**, изучите инструкции по устранению неполадок в статье [Гибридная рабочая роль Runbook. Задание Runbook завершается с состоянием "Приостановлено"](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

## <a name="relationship-to-service-management-automation"></a>Связь с автоматизацией управления службами
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) позволяет запускать в локальном центре обработки данных любые модули Runbook, поддерживаемые службой автоматизации Azure. Компонент SMA обычно развертывается вместе с Windows Azure Pack, так как Windows Azure Pack содержит графический интерфейс для управления SMA. В отличие от службы автоматизации Azure, компоненту SMA требуется локальная установка, которая включает в себя веб-серверы для размещения API, базу данных, содержащую модули Runbook и конфигурацию SMA, и рабочие роли runbook для выполнения заданий Runbook. Служба автоматизации Azure предоставляет эти службы в облаке, для чего необходимо поддерживать гибридные компоненты Runbook Worker в локальной среде.

Если вы являетесь существующим пользователем SMA, вы можете переместить ваши модели Runbook в службу автоматизации Azure, чтобы затем их использовать с гибридным компонентом Runbook Worker без изменений, предполагая, что они будут выполнять собственную проверку подлинности по отношению к ресурсам, как это описано в разделе [Создание модулей Runbook для гибридного компонента Runbook Worker](#creating-runbooks-for-hybrid-runbook-worker).  Модули Runbook в SMA выполняются в контексте учетной записи службы на сервере компонентов Worker, который может обеспечивать проверку подлинности для модулей Runbook.

Вы можете воспользоваться следующими критериями, чтобы определить, что больше подходит под ваши требования: служба автоматизации Azure с гибридным компонентом Runbook Worker или автоматизация управления службами (SMA).

* Если требуется графический интерфейс управления SMA, то нужно локально установить его базовые компоненты, связанные с Windows Azure Pack. По сравнению со службой автоматизации Azure, для которой необходим только агент, установленный в локальных рабочих ролях Runbook, требуется больше локальных ресурсов и более высокие затраты на обслуживание. Агенты управляются при помощи Operations Management Suite, что позволяет дополнительно снизить затраты на обслуживание.
* Служба автоматизации Azure сохраняет свои модули Runbook в облаке и предоставляет их локальным гибридным рабочим ролям Runbook. Если ваша политика безопасности не допускает развертывания такой модели, тогда необходимо использовать SMA.
* Компонент SMA входит в состав System Center. Следовательно, для его использования требуется лицензия System Center 2012 R2. Служба автоматизации Azure основана на модели многоуровневой подписки.
* Служба автоматизации Azure обладает дополнительными функциями, такими как создание графических модулей Runbook, которые недоступны в SMA.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о разных методах запуска модуля см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).  
* Описание разных методов работы с модулями Runbook PowerShell и рабочими процессами PowerShell в службе автоматизации Azure с использованием текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).

