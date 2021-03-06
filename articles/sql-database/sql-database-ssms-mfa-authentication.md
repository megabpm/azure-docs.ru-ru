---
title: "Многофакторная идентификация для SQL Azure | Документация Майкрософт"
description: "Использование Multi-Factor Authentication с SSMS для базы данных SQL и хранилища данных SQL."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 33282f1ce0c31056524359aeb42399be550334e2
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Универсальная проверка подлинности для Базы данных SQL и хранилища данных SQL (поддержка SSMS для MFA)
База данных SQL Azure и хранилище данных SQL Azure поддерживают подключения из SQL Server Management Studio (SSMS) с использованием *универсальной проверки подлинности Active Directory*. 

- Универсальная проверка подлинности Active Directory поддерживает два неинтерактивных метода проверки подлинности (проверку пароля Active Directory и встроенную аутентификацию Active Directory). Неинтерактивные методы проверки подлинности (проверку пароля Active Directory и встроенную аутентификацию Active Directory) можно использовать во множестве различных приложений (ADO.NET, JDBC, ODBC и т. д.). При использовании этих двух методов никогда не отображаются всплывающие диалоговые окна.

- Универсальная проверка подлинности Active Directory — это интерактивный метод, который также поддерживает *Многофакторную идентификацию Azure* (MFA). Azure MFA помогает защитить доступ к данным и приложениям, при этом не усложняя процесс входа пользователей в систему. Она обеспечивает надежную аутентификацию с использованием ряда простых вариантов подтверждения: телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении. Пользователи сами выбирают предпочтительный способ подтверждения. Интерактивная MFA с использованием Azure AD может привести к появлению всплывающего диалогового окна для реализации проверки.

Общие сведения о многофакторной идентификации см. в [этой статье](../multi-factor-authentication/multi-factor-authentication.md).
Инструкции по настройке см. в разделе [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Параметр доменного имени или идентификатора клиента Azure AD   

Начиная с [SSMS версии 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), пользователи, импортируемые в текущий каталог Active Directory из других каталогов Azure Active Directory, могут при подключении указывать доменное имя или идентификатор клиента Azure AD. Это позволяет **универсальной проверке подлинности Active Directory** определять правильный центр аутентификации. Этот параметр также является обязательным для поддержки учетных записей Майкрософт (MSA), таких как outlook.com, hotmail.com или live.com. Все пользователи, которые хотят выполнить аутентификацию с помощью универсальной проверки подлинности, должны ввести свое доменное имя или идентификатор клиента Azure AD. Этот параметр представляет собой текущее значение доменного имени или идентификатора клиента Azure AD, с которым связан сервер Azure. Например, если сервер Azure связан с доменом Azure AD `contosotest.onmicrosoft.com`, где пользователь `joe@contosodev.onmicrosoft.com` размещен в качестве импортированного пользователя из домена Azure AD `contosodev.onmicrosoft.com`, то доменное имя, необходимое для аутентификации этого пользователя, — `contosotest.onmicrosoft.com`. Если пользователь является собственным пользователем Azure AD, связанным с сервером Azure, а не пользователем с учетной записью MSA, то доменное имя или идентификатор клиента не требуются. Для ввода параметра (начиная с SSMS версии 17) в диалоговом окне **Подключение к базе данных** заполните поля, выбрав **Универсальная проверка подлинности Active Directory**. Щелкните **Параметры**, выберите вкладку **Свойства подключения**, установите флажок **Доменное имя AD или идентификатор клиента** и укажите центр аутентификации, например доменное имя (**contosotest.onmicrosoft.com**) или GUID идентификатора клиента.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Ограничения универсальной аутентификации для базы данных SQL и хранилища данных SQL
* SSMS — единственное средство, в настоящее время поддерживающее MFA с помощью универсальной аутентификации Active Directory.
* Только одна учетная запись Azure Active Directory может войти в экземпляр SSMS с использованием универсальной аутентификации. Чтобы войти с другой учетной записью Azure AD, необходимо использовать другой экземпляр SSMS. (Это ограничение относится только к универсальной аутентификации Active Directory. Вы можете войти на разные серверы, используя проверку пароля Active Directory, встроенную аутентификацию Active Directory или аутентификацию SQL Server).
* SSMS поддерживает универсальную аутентификацию Active Directory для отображения обозревателя объектов, редактора запросов и хранилища запросов.
* Ни конструктор схем, ни DacFx не поддерживают универсальную аутентификацию.
* Для универсальной аутентификации Active Directory не требуется дополнительное программное обеспечение, за исключением того, что необходимо использовать поддерживаемую версию SSMS.


## <a name="next-steps"></a>Дальнейшие действия

* Инструкции по настройке см. в разделе [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Предоставьте другим пользователям доступ к своей базе данных: [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).  
Предоставьте другим пользователям возможность подключаться через брандмауэр: [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).
* Сведения о настройке и управлении Azure AD см. в разделе [Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL или хранилища данных SQL](sql-database-aad-authentication-configure.md).



