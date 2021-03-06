---
title: "Подключение компьютера к виртуальной сети Azure с помощью соединения типа &quot;точка — сеть&quot; и портала Azure (классическое развертывание) | Документация Майкрософт"
description: "Безопасно подключайтесь к классической виртуальной сети Azure, создав VPN-подключение шлюза типа &quot;точка — сеть&quot; с помощью портала Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: b8e4bf060202c2700d8bf6cadedd13fc20783799
ms.contentlocale: ru-ru
ms.lasthandoff: 06/17/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Настройка подключения типа "точка — сеть" к виртуальной сети с помощью портала Azure (классическая модель)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

В этой статье показано, как создать виртуальную сеть с подключением типа "точка-сеть" в классической модели развертывания с помощью портала Azure. Эту конфигурацию также можно создать с помощью разных средств или моделей развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [Портал Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Портал Azure (классический)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Конфигурация типа "точка — сеть" позволяет создать безопасное подключение к виртуальной сети с отдельного клиентского компьютера. Это эффективное решение для подключений типа "точка — сеть" к виртуальной сети из удаленного расположения, например, если вы находитесь дома или на конференции, или если подключение к виртуальной сети требуется всего нескольким клиентам. VPN-подключение типа "точка — сеть" инициируется клиентским компьютером с помощью собственного VPN-клиента Windows. Для проверки подлинности подключений клиентов используются сертификаты. 


![Схема подключения типа "точка — сеть"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Для подключения типа "точка — сеть" не требуется VPN-устройство или общедоступный IP-адрес. Подключение типа "точка — сеть" — это VPN-подключение по протоколу SSTP (Secure Socket Tunneling Protocol). На стороне сервера поддерживается SSTP версии 1.0, 1.1 и 1.2. Какую версию использовать, решает клиент. Для Windows 8.1 и более поздних версий по умолчанию используется SSTP версии 1.2. Дополнительные сведения о подключениях типа "точка — сеть" см. в разделе [Часто задаваемые вопросы о подключениях типа "точка — сеть"](#faq) в конце этой статьи.

Для подключений типа "точка — сеть" требуется следующее:

* Динамический VPN-шлюз.
* Открытый ключ (CER-файл) для корневого сертификата, импортированный в Azure. Он считается доверенным сертификатом и используется для проверки подлинности.
* Сертификат клиента создается из корневого сертификата и устанавливается на каждом подключаемом клиентском компьютере. Этот сертификат используется для проверки подлинности клиента.
* Пакет конфигурации VPN-клиента необходимо создать и установить на каждом подключаемом клиентском компьютере. Пакет конфигурации клиента настраивает собственный VPN-клиент, находящийся в операционной системе, используя данные, необходимые для подключения к виртуальной сети.

### <a name="example-settings"></a>Примеры настроек

Следующие значения можно использовать для создания тестовой среды или для лучшего понимания примеров в этой статье.

* **Имя: VNet1**.
* **Адресное пространство: 192.168.0.0/16**.<br>В этом примере мы используем только одно адресное пространство, но для виртуальной сети можно настроить несколько.
* **Имя подсети: FrontEnd**.
* **Диапазон адресов подсети: 192.168.1.0/24**.
* **Подписка**. Если у вас есть несколько подписок, убедитесь, что используется правильная.
* **Группа ресурсов: TestRG**.
* **Расположение: восточная часть США**.
* **Тип подключения: "точка — сеть"**.
* **Адресное пространство клиента: 172.16.201.0/24**. VPN-клиенты, подключающиеся к виртуальной сети с помощью этого подключения "точка — сеть", получают IP-адреса из указанного пула.
* **Подсеть шлюза: 192.168.200.0/24**. Для подсети шлюза должно использоваться имя GatewaySubnet.
* **Размер.** Выберите SKU шлюза, который нужно использовать.
* **Тип маршрутизации: динамическая**.

## <a name="vnetvpn"></a>Раздел 1. Создание виртуальной сети и VPN-шлюза

Прежде чем начать, убедитесь в том, что у вас есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial).

### <a name="createvnet"></a>Часть 1. Создание виртуальной сети

Если у вас нет виртуальной сети, создайте ее. Снимки экрана приведены в качестве примеров. Обязательно подставьте собственные значения. Чтобы создать виртуальную сеть на портале Azure, сделайте следующее:

1. В браузере откройте [портал Azure](http://portal.azure.com) и при необходимости войдите с помощью учетной записи Azure.
2. Нажмите кнопку **Создать**. В поле **Поиск по Marketplace** введите "Виртуальная сеть". Найдите **виртуальную сеть** в результатах поиска и щелкните ее, чтобы открыть колонку **Виртуальная сеть**.

  ![Поиск колонки виртуальной сети](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. В нижней части колонки "Виртуальная сеть" в списке **Выберите модель развертывания** выберите **Классическая** и нажмите кнопку **Создать**.

  ![Выбор модели развертывания](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. В колонке **Создание виртуальной сети** настройте параметры виртуальной сети. В этой колонке добавьте первое адресное пространство и единый диапазон адресов подсети. После создания виртуальной сети вернитесь назад и добавьте дополнительные подсети и адресные пространства.

  ![Вкладка создания виртуальной сети](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Убедитесь, что вы используете правильную **подписку** . Подписки можно менять с помощью раскрывающегося списка.
6. Щелкните **Группа ресурсов** и либо выберите существующую, либо создайте новую группу ресурсов. Чтобы создать группу, введите ее имя. Если вы создаете группу ресурсов, укажите для нее имя, которое будет использоваться в вашей конфигурации. Дополнительные сведения о группах ресурсов см. в разделе "Группы ресурсов" [обзора Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Затем выберите параметры **расположения** для виртуальной сети. Расположение определяет, где будут находиться ресурсы, развертываемые в этой виртуальной сети.
8. Установите флажок **Закрепить на панели мониторинга** для быстрого перехода к виртуальной сети с панели мониторинга, а затем нажмите кнопку **Создать**.

  ![Закрепить на панели мониторинга](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. После нажатия кнопки "Создать" на панели мониторинга появится плитка, на которой отображается ход создания виртуальной сети. Когда виртуальная сеть будет создана, плитка изменится.

  ![Плитка создания виртуальной сети](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Когда виртуальная сеть будет создана, на классическом портале Azure на странице сетей в столбце **Состояние** отобразится значение **Создано**.
11. Добавьте DNS-сервер (необязательно). После создания виртуальной сети можно добавить IP-адрес DNS-сервера для разрешения имен. Следует указать DNS-сервер, который может разрешать имена ресурсов в виртуальной сети.<br>Чтобы добавить DNS-сервер, откройте параметры виртуальной сети, щелкните "DNS-серверы" и добавьте IP-адрес DNS-сервера, который нужно использовать. Пакет конфигурации клиента, который будет создан позже, будет содержать IP-адреса DNS-серверов, указанные для этого параметра. Если в будущем потребуется обновить список DNS-серверов, вы можете создать и установить новые пакеты конфигурации VPN-клиента, в которых будет отображен обновленный список.

### <a name="gateway"></a>Часть 2. Создание подсети шлюза и шлюза динамической маршрутизации

На этом шаге вы создадите подсеть шлюза и шлюз динамической маршрутизации. При создании подсети шлюза и шлюза на портале Azure для классической модели развертывания используются те же колонки конфигурации.

1. На портале перейдите к виртуальной сети, для которой необходимо создать шлюз.
2. В колонке виртуальной сети в колонке **Обзор** в разделе "VPN-подключения" щелкните **Шлюз**.

  ![Щелкните, чтобы создать шлюз.](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. В колонке **Новое VPN-подключение** выберите **Точка — сеть**.

  ![Подключение типа "точка — сеть"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. В поле **Адресное пространство клиента** добавьте диапазон IP-адресов. Из этого диапазона VPN-клиенты будут получать IP-адреса при подключении. Используйте диапазон частных IP-адресов, который не пересекается с локальным расположением, из которого будет выполняться подключение, или с виртуальной сетью, к которой вы хотите подключиться. Вы можете удалить автоматически заполненный диапазон, затем добавить частный диапазон IP-адресов, который хотите использовать.

  ![Адресное пространство клиента](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Установите флажок **Немедленно создать шлюз**.

  ![Немедленно создать шлюз](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Щелкните **Дополнительная настройка шлюза**, чтобы открыть колонку **Настройка шлюза**.

  ![Щелкните "Дополнительная настройка шлюза"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Щелкните **Subnet Configure required settings** (Обязательные параметры для настройки подсети), чтобы добавить **подсеть шлюза**. Хотя можно создать подсеть шлюза размером /29, рекомендуется создать подсеть большего размера, включающую несколько адресов, выбрав по крайней мере значение /28 или /27. Таким образом, у вас будет достаточно адресов, чтобы добавить дополнительные конфигурации в будущем. При работе с подсетями шлюза не связывайте группу безопасности сети (NSG) с подсетью шлюза. Связывание группы безопасности сети (NSG) с этой подсетью приведет к тому, что VPN-шлюз перестанет правильно функционировать.

  ![Добавление подсети шлюза](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Выберите **размер** шлюза. Размер — это SKU шлюза для шлюза виртуальной сети. На портале SKU по умолчанию — **Базовый**. Дополнительные сведения о номерах SKU шлюзов см. в статье [Сведения о параметрах VPN-шлюза](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Размер шлюза](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Выберите **тип маршрутизации** для шлюза. Конфигурации "точка — сеть" требует **динамического** типа маршрутизации. Нажмите кнопку **ОК** после завершения настройки в этой колонке.

  ![Настройка типа маршрутизации](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. В нижней части колонки **Новое VPN-подключение** нажмите кнопку **ОК**, чтобы приступить к созданию шлюза виртуальной сети. Создание VPN-шлюза может занять до 45 минут в зависимости от выбранного номера SKU шлюза.

## <a name="generatecerts"></a>Раздел 2. Создание сертификатов

Сертификаты используются в Azure для проверки подлинности VPN-клиентов в VPN-подключениях типа "точка — сеть". Необходимо отправить сведения об открытом ключе корневого сертификата в Azure. После этого открытый ключ считается доверенным. Сертификаты клиентов должны создаваться из доверенного корневого сертификата, а затем устанавливаться на каждом клиентском компьютере в хранилище сертификатов Certificates-Current User/Personal. Сертификат используется для проверки подлинности клиента, когда он инициирует подключение к виртуальной сети. Дополнительные сведения о создании и установке сертификатов см. в статье [Создание самозаверяющего корневого сертификата для подключений типа "точка — сеть" с помощью PowerShell](vpn-gateway-certificates-point-to-site.md).

### <a name="cer"></a>Часть 1. Получение открытого ключа CER-файла для корневого сертификата

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Часть 2. Создание сертификата клиента

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>Раздел 3. Отправка CER-файла корневого сертификата

После создания шлюза вы можете передать CER-файл (который содержит сведения об открытом ключе) доверенного корневого сертификата в Azure. Не отправляйте закрытый ключ для корневого сертификата в Azure. После отправки CER-файла Azure сможет использовать его для проверки подлинности клиентов, на которых установлен клиентский сертификат, созданный из доверенного корневого сертификата. При необходимости позже можно отправить дополнительные файлы доверенных корневых сертификатов (не более 20).  

1. В колонке виртуальной сети в разделе **VPN-подключения** щелкните рисунок, обозначающий **клиентов**, чтобы открыть колонку **VPN-подключение типа "точка — сеть"**.

  ![Клиенты](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. В колонке **VPN-подключение типа "точка — сеть"** щелкните **Управление сертификатами**, чтобы открыть колонку **Сертификаты**.<br>

  ![Колонка "Сертификаты"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. В колонке **Сертификаты** щелкните **Загрузить**, чтобы открыть колонку **Отправка сертификата**.<br>

    ![Колонка "Отправка сертификата"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Щелкните значок папки, чтобы найти CER-файл. Выберите файл и нажмите кнопку **ОК**. Обновите страницу, чтобы сертификат отобразился в колонке **Сертификаты**.

  ![Передача сертификата](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Раздел 4. Настройка клиентского компьютера

Для подключения к виртуальной сети с помощью VPN-подключения типа "точка — сеть" для каждого клиента необходимо установить пакет конфигурации для собственного VPN-клиента Windows. Пакет конфигурации настраивает собственный VPN-клиент Windows с использованием параметров, необходимых для подключения к виртуальной сети. Если для вашей виртуальной сети указан DNS-сервер, пакет содержит IP-адрес DNS-сервера, который будет использоваться клиентом для разрешения имен. Если вы решите изменить указанный DNS-сервер позже, после создания пакета конфигурации клиента, обязательно создайте новый пакет конфигурации клиента для установки на клиентских компьютерах.

На каждом клиентском компьютере можно использовать один и тот же пакет конфигурации VPN-клиента при условии, что его версия соответствует архитектуре клиента. Список поддерживаемых клиентских операционных систем см. в разделе [Часто задаваемые вопросы о подключениях типа "точка — сеть"](#faq) в конце этой статьи.

### <a name="part-1-generate-and-install-the-vpn-client-configuration-package"></a>Часть 1. Создание и установка пакета конфигурации VPN-клиента

1. На портале Azure в колонке **Обзор** виртуальной сети в разделе **VPN-подключения** щелкните рисунок, обозначающий клиентов, чтобы открыть колонку **VPN-подключение типа "точка — сеть"**.
2. В верхней части колонки **VPN-подключение типа "точка — сеть"** выберите пакет скачивания для операционной системы, в которой требуется установить клиент:

  * Для 64-разрядных клиентов выберите вариант **VPN-клиент (64-разрядная версия)**.
  * Для 32-разрядных клиентов выберите вариант **VPN-клиент (32-разрядная версия)**.

  ![Скачивание пакета конфигурации VPN-клиента](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. После создания пакета скачайте и установите его на клиентском компьютере. При появлении всплывающего окна SmartScreen щелкните **Дополнительно**, а затем выберите **Выполнить в любом случае**. Вы также можете сохранить пакет для установки на других клиентских компьютерах.

### <a name="part-2-install-the-client-certificate"></a>Часть 2. Установка сертификата клиента

Если вы хотите создать подключение типа "точка — сеть" на клиентском компьютере, отличном от того, который использовался для создания сертификатов клиентов, необходимо установить сертификат клиента. При установке сертификата клиента потребуется пароль, созданный при экспорте сертификата клиента. Обычно для этого нужно просто дважды щелкнуть сертификат и установить его. Дополнительные сведения см. в разделе [Установка экспортированного сертификата клиента](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>Часть 5. Подключение к Azure

### <a name="connect-to-your-vnet"></a>Подключение к виртуальной сети

1. Чтобы подключиться к виртуальной сети, откройте VPN-подключения на клиентском компьютере и найдите созданное VPN-подключение. Его имя совпадает с названием вашей виртуальной сети. Щелкните **Подключить**. Может появиться всплывающее сообщение об использовании сертификата. В таком случае щелкните **Продолжить** , чтобы использовать более высокий уровень привилегий.
2. На странице состояния **подключения** щелкните **Подключить**. Если появится окно **Выбор сертификата** , убедитесь в том, что отображается сертификат клиента, с помощью которого вы хотите подключиться к сети. Если окно не появится, выберите нужный сертификат в раскрывающемся списке и нажмите кнопку **ОК**.

  ![Подключение VPN-клиента](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Теперь подключение установлено.

  ![Установленное подключение](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

Если возникают проблемы с подключением, проверьте следующее:

- Откройте раздел **Управление сертификатами пользователей** и перейдите к пункту **Доверенные корневые центры сертификации\Сертификаты**. Убедитесь, что корневой сертификат есть в списке. В противном случае проверка подлинности не будет выполнена. При экспорте PFX-файла сертификата клиента с использованием значения по умолчанию "Включить по возможности все сертификаты в путь сертификации" также экспортируются данные корневого сертификата. При установке сертификата клиента корневой сертификат также устанавливается на клиентском компьютере. 

- Если используется сертификат, который был выдан с помощью корпоративного ЦС, и при аутентификации возникли проблемы, проверьте порядок прохождения аутентификации в сертификате клиента. Проверить список этапов аутентификации можно, дважды щелкнув сертификат клиента и выбрав **Сведения > Улучшенный ключ**. Убедитесь, что первым в списке отображается пункт "Проверка подлинности клиента" . В противном случае потребуется выдать сертификат клиента на основе шаблона User, в котором пункт "Проверка подлинности клиента" указан первым в списке. 

### <a name="verify-the-vpn-connection"></a>Проверка VPN-подключения

1. Чтобы проверить, активно ли VPN-подключение, откройте окно командной строки от имени администратора и выполните команду *ipconfig/all*.
2. Просмотрите результаты. Обратите внимание, что полученный вами IP-адрес — это один из адресов в адресном пространстве подключения типа "точка — сеть", которое вы указали при создании виртуальной сети. Результаты должны выглядеть примерно так:

Пример:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

 
 Если возникают проблемы при подключении к виртуальной машине по типу "точка — сеть", используйте ipconfig, чтобы проверить IPv4-адрес, назначенный Ethernet-адаптеру на компьютере, с которого выполняется подключение. Если IP-адрес находится в диапазоне адресов виртуальной сети, к которой выполняется подключение, или в диапазоне адресов VPNClientAddressPool, адресное пространство перекрывается. В таком случае сетевой трафик не достигает Azure и остается в локальной сети. Если адресные пространства виртуальной сети не перекрываются и вы по-прежнему не можете подключиться к виртуальной машине, см. сведения в статье [Устранение неполадок с подключением к виртуальной машине Azure через удаленный рабочий стол](../virtual-machines/windows/troubleshoot-rdp-connection.md).

## <a name="connectVM"></a>Подключение к виртуальной машине

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Добавление и удаление доверенного корневого сертификата

Вы можете добавлять доверенные корневые сертификаты в Azure, а также удалять их из Azure. При удалении корневого сертификата клиенты, использующие сертификат, созданный из этого корневого сертификата, не смогут пройти проверку подлинности и поэтому не смогут подключиться. Чтобы клиенты могли проходить аутентификацию и подключаться, необходимо установить новый сертификат клиента, созданный на основе корневого сертификата, который является доверенным для Azure (то есть он передан в Azure).

### <a name="to-add-a-trusted-root-certificate"></a>Добавление доверенного корневого сертификата

В Azure можно добавить до 20 CER-файлов доверенных корневых сертификатов. Инструкции см. в разделе 3 [Отправка CER-файла корневого сертификата](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Удаление доверенного корневого сертификата

1. В колонке виртуальной сети в разделе **VPN-подключения** щелкните рисунок, обозначающий **клиентов**, чтобы открыть колонку **VPN-подключение типа "точка — сеть"**.

  ![Клиенты](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. В колонке **VPN-подключение типа "точка — сеть"** щелкните **Управление сертификатами**, чтобы открыть колонку **Сертификаты**.<br>

  ![Колонка "Сертификаты"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. В колонке **Сертификаты** нажмите кнопку с многоточием рядом с сертификатом, который требуется удалить, а затем щелкните **Удалить**.

  ![Удаление корневого сертификата](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Отзыв сертификата клиента

Можно отозвать сертификаты клиента. Список отзыва сертификатов позволяет выборочно запрещать подключение типа "точка-сеть" на основе отдельных сертификатов клиента. Эта процедура отличается от удаления доверенного корневого сертификата. При удалении доверенного корневого сертификата (CER-файл) из Azure будет запрещен доступ для всех сертификатов клиента, созданных на основе отозванного корневого сертификата или подписанных им. Отзыв сертификата клиента, а не корневого сертификата, позволяет по-прежнему использовать другие сертификаты, созданные на основе корневого сертификата, для проверки подлинности подключения типа "точка — сеть".

Обычно корневой сертификат используется для управления доступом на уровнях группы или организации, а отозванный сертификат клиента — для точного контроля доступа для отдельных пользователей.

### <a name="to-revoke-a-client-certificate"></a>Отзыв сертификата клиента

Вы можете отозвать сертификат клиента, добавив отпечаток в список отзыва.

1. Получите отпечаток сертификата клиента. Дополнительные сведения см. в статье [Практическое руководство. Извлечение отпечатка сертификата](https://msdn.microsoft.com/library/ms734695.aspx).
2. Скопируйте данные в текстовый редактор и удалите все пробелы, чтобы предоставить отпечаток в виде непрерывной строки.
3. Перейдите к колонке **имя классической виртуальной сети > VPN-подключение типа "точка — сеть" > Сертификаты** и выберите **Список отзыва**, чтобы открыть колонку списка отзыва. 
4. В колонке **Список отзыва** щелкните **+Add certificate** (Добавить сертификат), чтобы открыть колонку **Добавление сертификата в список отзыва**.
5. В колонке **Добавление сертификата в список отзыва** вставьте отпечаток сертификата в виде одной непрерывной текстовой строки без пробелов. В нижней части колонки нажмите кнопку **ОК**.
6. После применения изменений сертификат больше не будет использоваться для подключения. Клиенты, пытающиеся подключиться с помощью этого сертификата, получат сообщение, что он недействителен.

## <a name="faq"></a>Часто задаваемые вопросы о подключениях типа "точка — сеть"

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Дополнительные сведения о сетях и виртуальных машинах см. в статье [Azure и Linux: обзор сетей виртуальных машин](../virtual-machines/linux/azure-vm-network-overview.md).

