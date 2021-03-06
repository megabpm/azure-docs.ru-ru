---
title: "Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью портала Azure | Документация Майкрософт"
description: "Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью портала Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 744f3503d2abc618095351e1b018cab22adcf091
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-the-azure-portal"></a>Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью портала Azure
Правила брандмауэра уровня сервера позволяют администраторам обращаться к серверу базы данных Azure для MySQL с указанного IP-адреса или диапазона IP-адресов. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Создание правила брандмауэра на уровне сервера с помощью портала Azure

1. В колонке сервера MySQL в разделе "Параметры" щелкните **Безопасность подключения**, чтобы открыть колонку "Безопасность подключения" базы данных Azure для MySQL.
![Портал Azure: щелчок пункта "Безопасность подключения"](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Нажмите кнопку **Добавить мой IP-адрес** на панели инструментов. Автоматически будет создано правило с IP-адресом компьютера, определенным системой Azure.
![Портал Azure: нажатие кнопки "Добавить Мой IP-адрес"](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Проверьте IP-адрес перед сохранением конфигурации. В некоторых случаях IP-адрес, определенный порталом Azure, может отличаться от IP-адреса, используемого для доступа к Интернету и серверам Azure. Поэтому может потребоваться изменить начальный IP-адрес и конечный IP-адрес для правила, чтобы оно функционировало ожидаемым образом.

Используйте поисковую систему или другой сетевой инструмент, чтобы проверить собственный IP-адрес (например, выполните поиск текста "what is my IP").
![Поиск текста "what is my IP" в Bing](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. На панели инструментов нажмите кнопку **Сохранить**, чтобы сохранить это правило брандмауэра уровня сервера. Дождитесь подтверждения успешного обновления правил брандмауэра.
![Портал Azure: нажатие кнопки "Сохранить"](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

> [!NOTE]
> В правилах брандмауэра базы данных Azure для MySQL можно указать отдельный IP-адрес или диапазон адресов. Если вы хотите ограничить правило, указав отдельный IP-адрес, введите его в полях начального и конечного IP-адресов. Открыв брандмауэр, вы дадите возможность администраторам и пользователям входить в любую базу данных на сервере MySQL, для которой у них есть действительные учетные данные.
![Портал Azure: правила брандмауэра](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Управление существующими правилами брандмауэра с помощью портала Azure
Повторите действия для управлению правилами брандмауэра.
* Чтобы добавить текущий компьютер, нажмите кнопку **Добавить мой IP-адрес**.
* Чтобы добавить дополнительные IP-адреса, введите **имя правила**, **начальный IP-адрес** и **конечный IP-адрес** в соответствующих полях.
* Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
* Чтобы удалить существующее правило, щелкните многоточие [...] и выберите **Удалить**.
* Щелкните **Сохранить** , чтобы сохранить изменения.

## <a name="next-steps"></a>Дальнейшие действия
- Справка по подключению к серверу базы данных Azure для MySQL доступна в разделе [Библиотеки подключений для базы данных Azure для MySQL](./concepts-connection-libraries.md).
