---
title: "Создание или обновление шлюза приложений Azure с добавлением брандмауэра веб-приложения | Документация Майкрософт"
description: "Узнайте, как создать шлюз приложений с брандмауэром веб-приложения с помощью портала."
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 224aa0db2feb7a83bec5b4ec46140046d10f012e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/04/2017


---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Создание шлюза приложений с брандмауэром веб-приложения с помощью портала

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-web-application-firewall-powershell.md)

Узнайте, как создать шлюз приложений с брандмауэром веб-приложения.

Брандмауэр веб-приложения (WAF) в шлюзе приложений Azure защищает веб-приложения от таких распространенных сетевых атак, как атаки путем внедрения кода SQL, атаки межсайтовых сценариев и захваты сеанса. Веб-приложение обеспечивает защиту от многих распространенных сетевых уязвимостей в рамках проекта OWASP.

## <a name="scenarios"></a>Сценарии

В этой статье представлено два сценария.

Во первом сценарии рассматривается [создание шлюза приложений с брандмауэром веб-приложения](#create-an-application-gateway-with-web-application-firewall).

Во втором сценарии рассматривается [добавление брандмауэра веб-приложения в существующий шлюз приложений](#add-web-application-firewall-to-an-existing-application-gateway).

![Пример сценария][scenario]

> [!NOTE]
> Дополнительная настройка шлюза приложений, включая пользовательские пробы работоспособности, серверный пул адресов и дополнительные правила, осуществляется после настройки шлюза приложений, а не во время первоначального развертывания.

## <a name="before-you-begin"></a>Перед началом работы

Шлюзу приложений Azure требуется собственная подсеть. При создании виртуальной сети обязательно оставьте достаточно адресного пространства для нескольких подсетей. После развертывания шлюза приложений в подсети в нее можно добавлять только дополнительные шлюзы приложений.

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Добавление брандмауэра веб-приложения в существующий шлюз приложений

В этом примере существующий шлюз приложений обновляется для поддержки брандмауэра веб-приложения, работающего в режиме предотвращения.

1. На портале Azure в области **Избранное** щелкните **Все ресурсы**. Выберите существующий шлюз приложений в колонке **Все ресурсы**. Если выбранная подписка имеет несколько ресурсов, в поле **Фильтровать по имени...** введите имя, чтобы быстро получить доступ к необходимой зоне DNS.

   ![Создание шлюза приложений][1]

1. Щелкните **Брандмауэр веб-приложения** и обновите параметры шлюза приложений. По завершении нажмите кнопку **Сохранить**

    Ниже перечислены параметры, позволяющие обновить шлюз приложений для поддержки брандмауэра веб-приложения.

   | **Параметр** | **Значение** | **Дополнительные сведения**
   |---|---|---|
   |**Обновление до уровня WAF**| Флажок установлен | Этот параметр задает для шлюза приложений уровень WAF.|
   |**Состояние брандмауэра**| Включено | Включено | Этот параметр включает для брандмауэра уровень WAF.|
   |**Режим брандмауэра** | Предотвращение | Этот параметр определяет, как брандмауэр веб-приложения поступает с вредоносным трафиком. В режиме **Обнаружение** события только регистрируются в журнале, тогда как в режиме **Предотвращение** события регистрируются в журнале и останавливается вредоносный трафик.|
   |**Набор правил**|3.0|Этот параметр определяет [основной набор правил](application-gateway-web-application-firewall-overview.md#core-rule-sets), используемый для защиты элементов внутреннего пула.|
   |**Настройка отключенных правил**|различные|Чтобы предотвратить возможные ложные срабатывания, этот параметр позволяет отключить определенные [правила и группы правил](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > При обновлении существующего шлюза приложений до номера SKU WAF размер SKU изменяется на **Средний**. Это можно изменить после завершения настройки.

    ![Колонка основных параметров][2-1]

    > [!NOTE]
    > Чтобы иметь возможность просматривать журналы брандмауэра веб-приложения, необходимо включить систему диагностики и выбрать параметр ApplicationGatewayFirewallLog. Для тестирования можно выбрать число экземпляров, равное 1. Важно знать, что если указать число экземпляров менее двух, то развертывание не попадает под действие соглашения об уровне обслуживания, и поэтому это не рекомендуется. При использовании брандмауэра веб-приложения шлюзы уровня "Мелкий" недоступны.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>создание шлюза приложений с брандмауэром веб-приложения

Вы узнаете:

* как создать средний шлюз приложений с брандмауэром веб-приложения и двумя экземплярами;
* как создать виртуальную сеть AdatumAppGatewayVNET с зарезервированным блоком CIDR (10.0.0.0/16);
* как создать подсеть с именем Appgatewaysubnet и блоком CIDR (10.0.0.0/28);
* как настроить сертификат для разгрузки SSL.

1. Войдите на [портал Azure](https://portal.azure.com). Если у вас нет учетной записи, вы можете зарегистрироваться для получения [бесплатной пробной версии на один месяц](https://azure.microsoft.com/free).
1. В области "Избранное" портала нажмите кнопку **Создать**.
1. В колонке **Создать** щелкните **Сети**. В колонке **Сети** щелкните **Шлюз приложений**, как показано на следующем изображении.
1. Перейдите на портал Azure и выберите **Создать** > **Сети** > **Шлюз приложений**.

    ![Создание шлюза приложений][1]

1. В открывшейся колонке **Основное** введите приведенные ниже значения и нажмите кнопку **ОК**.

   | **Параметр** | **Значение** | **Дополнительные сведения**
   |---|---|---|
   |**Имя**|AdatumAppGateway|Имя шлюза приложений.|
   |**Уровень**|WAF|Доступные значения: "Стандартный" или "WAF". Ознакомьтесь с [брандмауэром веб-приложения](application-gateway-web-application-firewall-overview.md), чтобы узнать больше о WAF.|
   |**Размер номера SKU**|Средний|При выборе уровня "Стандартный" доступны мелкий, средний и большой размеры. При выборе уровня "WAF" можно выбрать только средний или большой размер.|
   |**Число экземпляров**|2|Число экземпляров шлюза приложений для обеспечения высокого уровня доступности. Количество экземпляров, равное 1, следует использовать только для тестирования.|
   |**Подписка**|[Ваша подписка]|Выберите подписку для создания шлюза приложений.|
   |**Группа ресурсов**|**Создать:** AdatumAppGatewayRG|Создайте группу ресурсов. Имя группы ресурсов должно быть уникальным в пределах выбранной подписки. Дополнительные сведения о группах ресурсов см. в разделе [Группы ресурсов](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) статьи "Общие сведения об Azure Resource Manager".|
   |**Расположение**|Запад США||

   ![Колонка основных параметров][2-2]

1. В колонке **Параметры**, которая отображается под областью **Виртуальная сеть**, щелкните **Выберите виртуальную сеть**. Откроется колонка **Выбор виртуальной сети**.  Щелкните **Создать**, чтобы открыть колонку **Создание виртуальной сети**.

   ![Выбор виртуальной сети][2]

1. В колонке **Создание виртуальной сети** введите приведенные ниже значения и нажмите кнопку **Создать**. Колонки **Создание виртуальной сети** и **Выбор виртуальной сети** закроются. Кроме того, в поле **Подсеть** в колонке **Параметры** будет указана выбранная подсеть.

   |**Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Имя**|AdatumAppGatewayVNET|Имя шлюза приложений.|
   |**Адресное пространство**|10.0.0.0/16| Это адресное пространство виртуальной сети.|
   |**Имя подсети**|AppGatewaySubnet|Имя подсети для шлюза приложений.|
   |**Диапазон адресов подсети**|10.0.0.0/28| Эта подсеть позволяет создать дополнительные подсети в виртуальной сети для членов внутреннего пула.|

1. В колонке **Параметры** в разделе **Интерфейсная IP-конфигурация** выберите для параметра **Тип IP-адреса** значение **Общедоступный**.

1. В колонке **Параметры** в разделе **Общедоступный IP-адрес** щелкните **Выберите общедоступный IP-адрес**. Откроется колонка **Выбрать общедоступный IP-адрес**. Щелкните **Создать**.

   ![Выбор общедоступного IP-адреса][3]

1. В колонке **Создать общедоступный IP-адрес** примите значение по умолчанию и нажмите кнопку **ОК**. Колонки **Выбрать общедоступный IP-адрес** и **Создать общедоступный IP-адрес** закроются, а в поле **Общедоступный IP-адрес** будет указан выбранный общедоступный IP-адрес.

1. В колонке **Параметры** в разделе **Конфигурация прослушивателя** для параметра **Протокол** щелкните **HTTPS**. Например, для использования **HTTPS**требуется сертификат. Нужен закрытый ключ сертификата, поэтому необходимо предоставить экспортированный PFX-файл сертификата и пароль для него.

1. Настройте определенные параметры **WAF** .

   |**Параметр** | **Значение** | **Дополнительные сведения** |
   |---|---|---|
   |**Состояние брандмауэра**| Включено| Этот параметр позволяет включить или отключить WAF.|
   |**Режим брандмауэра** | Предотвращение| Этот параметр определяет действия, предпринимаемые WAF в отношении вредоносного трафика. Если выбран режим **Обнаружение**, то трафик только регистрируются в журнале.  Если выбран режим **Предотвращение**, то трафик регистрируется в журнале и останавливается с выводом ответа "403 — не авторизовано".|


1. Просмотрите страницу сводки и нажмите кнопку **ОК**.  Теперь шлюз приложений поставлен в очередь и создан.

1. После создания шлюза приложений перейдите к нему на портале, чтобы продолжить настройку шлюза параметров.

    ![Представление ресурса шлюза приложений][10]

В результате выполнения этих действий создается базовый шлюз приложений с параметрами по умолчанию для прослушивателя, серверного пула, протокола HTTP серверной части и правил. Вы сможете изменить эти параметры в соответствии с развертыванием после успешного завершения подготовки.

> [!NOTE]
> Для защиты шлюзов приложений, созданных с помощью базовой конфигурации брандмауэра веб-приложения, настраивается CRS 3.0.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как настроить ведение журнала диагностики и как регистрировать в журнале события, которые обнаружил или предотвратил брандмауэр веб-приложения, посетив страницу [Мониторинг работоспособности серверной части, ведение журнала диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

Узнайте, как создавать пользовательские пробы работоспособности, посетив страницу [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Узнайте, как настроить разгрузку SSL и убрать дорогостоящее шифрование SSL с веб-серверов, посетив страницу [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

