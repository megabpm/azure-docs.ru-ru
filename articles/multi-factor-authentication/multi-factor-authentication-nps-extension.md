---
title: "Использование существующих серверов NPS для реализации возможностей MFA Azure | Документация Майкрософт"
description: "Расширение сервера политики сети для многофакторной идентификации Azure — это простое решение, позволяющее добавить двухэтапную облачную проверку пользователей в существующую инфраструктуру проверки подлинности."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 58b289530e16c2a2e9bbe59b372c858ff22ad5ac
ms.contentlocale: ru-ru
ms.lasthandoff: 04/29/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>Интеграция имеющейся инфраструктуры NPS со службой Многофакторной идентификации Azure (общедоступная предварительная версия)

Расширение сервера политики сети (NPS) для MFA Azure добавляет облачные функции многофакторной идентификации в инфраструктуру проверки подлинности на базе существующих серверов. Расширение NPS позволяет добавить телефонный звонок, текстовое сообщение или запрос на подтверждение в мобильном приложении в существующий процесс проверки подлинности, позволяя обойтись без установки, настройки и поддержания новых серверов. 

Это расширение было создано для организаций, которым необходимо защитить VPN-подключения, не развертывая сервер Azure MFA. Расширение NPS выполняет функции адаптера между RADIUS и облачной службой Azure MFA, предоставляя второй фактор проверки подлинности для федеративных или синхронизированных пользователей. 

Если вы используете расширение NPS для многофакторной идентификации Azure, процесс проверки подлинности состоит из следующих компонентов. 

1. **NAS или VPN-сервер** получает запросы от клиентов, использующих VPN-подключение, и преобразует их в запросы RADIUS, направляемые на серверы политики сети. 
2. **Сервер политики сети** подключается к Active Directory и выполняет первичную проверку подлинности по запросам RADIUS. В случае успеха он передает запрос в установленные расширения.  
3. **Расширение NPS** активирует запрос к MFA Azure для проведения дополнительной проверки подлинности. Когда расширение получает ответ (при условии, что запрос MFA выполнен успешно), расширение завершает процесс проверки подлинности, возвращая серверу NPS маркеры безопасности, которые включают утверждение многофакторной идентификации, выданное службой маркеров безопасности Azure.  
4. **Azure MFA** обращается к Azure Active Directory для получения сведений о пользователе и выполняет дополнительную проверку подлинности по тому методу, который настроен для этого пользователя.

На следующей схеме обобщенно представлена последовательность запросов на проверку подлинности. 

![Схема последовательности проверки подлинности](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Планирование развертывания

Расширение NPS автоматически обрабатывает избыточность, поэтому специальная настройка не требуется. 

Можно создать столько NPS-серверов с поддержкой Многофакторной идентификации Azure, сколько требуется. При установке нескольких серверов для каждого из них следует использовать отдельный сертификат клиента. Создание сертификата для каждого сервера означает, что можно обновлять каждый сертификат по отдельности и не беспокоиться об одновременных простоях всех серверов. 

VPN-серверы перенаправляют запросы на аутентификацию, поэтому они должны знать о новых NPS-серверах с поддержкой Многофакторной идентификации Azure. 

## <a name="prerequisites"></a>Предварительные требования

Расширение NPS предназначено для работы с существующей инфраструктурой. Прежде чем приступить к работе, убедитесь, что у вас есть следующие необходимые компоненты.

### <a name="licenses"></a>Лицензии

Расширение NPS для многофакторной проверки подлинности Azure доступно для клиентов с [лицензией на MFA Azure](multi-factor-authentication.md) (входит в состав подписок Azure AD Premium, EMS и MFA).

### <a name="software"></a>Программное обеспечение

Windows Server 2008 R2 с пакетом обновления 1 (SP1) или более поздняя версия.

### <a name="libraries"></a>Библиотеки

Эти библиотеки устанавливаются автоматически вместе с расширением. 
-    [Распространяемые пакеты Visual C++ для Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Модуль Microsoft Azure Active Directory для Windows PowerShell, версия 1.1.166](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

Все пользователи, использующие расширение NPS, должны синхронизироваться с Azure Active Directory с помощью Azure AD Connect, и для них должна быть включена многофакторная идентификация.

Для установки расширения потребуются учетные данные администратора и идентификатор каталога для вашего клиента Azure AD. Идентификатор каталога вы можете найти на [портале Azure](https://portal.azure.com). Войдите с учетными данными администратора, выберите значок **Azure Active Directory** слева, а затем выберите **Свойства**. Скопируйте значение GUID в поле **Directory ID** (Идентификатор каталога) и сохраните его. Этот GUID будет использоваться как идентификатор клиента при установке расширения NPS.

![Поиск идентификатора каталога в разделе свойств для Azure Active Directory](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Подготовка среды

Прежде чем устанавливать расширение NPS, следует подготовить среду для обработки трафика аутентификации. 

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Включение роли NPS на сервере, присоединенном к домену

NPS-сервер подключается к Azure Active Directory и аутентифицирует запросы MFA. Выберите один сервер для этой роли. Рекомендуется выбрать сервер, который не обрабатывает запросы от других служб, так как расширение NPS выдает ошибки для всех запросов, которые не являются запросами RADIUS.

1. Откройте на сервере **мастер добавления ролей и компонентов**, воспользовавшись меню быстрого запуска диспетчера сервера.
2. Щелкните **Установка ролей или компонентов** для используемого типа установки.
3. Выберите роль сервера **Службы политики сети и доступа**. Может появиться окно, сообщающее о необходимых компонентах для выполнения этой роли.
4. Следуйте дальнейшим указаниям мастера, пока не перейдете к странице подтверждения. Щелкните **Установить**. 

Теперь назначенный NPS-сервер следует настроить для обработки входящих запросов RADIUS от решения VPN. 

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Настройка решения VPN для обмена данными с NPS-сервером

В зависимости от того, какое решение VPN используется, действия по настройке политики аутентификации RADIUS могут отличаться. Настройте эту политику, указав NPS-сервер RADIUS. 

### <a name="sync-domain-users-to-the-cloud"></a>Синхронизация пользователей домена с облаком

Возможно, этот шаг уже выполнен в клиенте, но рекомендуется еще раз убедиться, что служба Azure AD Connect недавно синхронизировала базы данных. 

1. Войдите на [портал Azure](https://portal.azure.com) с использованием учетной записи администратора.
2. Щелкните **Azure Active Directory** > **Azure AD Connect**.
3. Убедитесь в том, что синхронизация находится в состоянии **Включена** и последняя синхронизация была выполнена менее часа назад.

Если требуется начать новый цикл синхронизации, следуйте инструкциям в разделе [Синхронизация Azure AD Connect: планировщик](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="enable-users-for-mfa"></a>Включение MFA для пользователей

Перед полным развертыванием расширения NPS необходимо включить MFA для пользователей, которым следует проходить двухфакторную проверку подлинности. Сейчас же, чтобы проверить расширения после развертывания, необходима по крайней мере одна тестовая учетная запись, полностью зарегистрированная для использования Многофакторной идентификации. 

Чтобы приступить к работе с тестовой учетной записью, выполните следующее.
1. [Включите MFA для учетной записи](multi-factor-authentication-get-started-user-states.md).
2. Перейдите на любой веб-сайт, который запускает аутентификацию Azure AD, например https://portal.azure.com.
3. [Зарегистрируйтесь для использования двухфакторной проверки подлинности](./end-user/multi-factor-authentication-end-user-first-time.md).

Пользователям также необходимо выполнить эти действия для регистрации, чтобы проходить проверку подлинности с помощью расширения NPS.

## <a name="install-the-nps-extension"></a>Установка расширения NPS

> [!IMPORTANT]
> Установите расширение NPS на другом сервере, который не является точкой доступа VPN. 

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Скачивание и установка расширения NPS для MFA Azure 

1.    [Скачайте расширение NPS](https://aka.ms/npsmfa) из Центра загрузки Майкрософт.
2.    Скопируйте этот двоичный файл на сервер политики сети, который требуется настроить.
3.    Запустите файл *setup.exe* и следуйте инструкциям по установке. При возникновении ошибок проверьте, были ли успешно установлены две библиотеки из раздела необходимых компонентов.

### <a name="run-the-powershell-script"></a>Запуск сценария PowerShell

Установщик создаст сценарий PowerShell в этом расположении: `C:\Program Files\Microsoft\AzureMfa\Config` (где C:\ — диск установки). Скрипт PowerShell выполняет следующие действия.

-    Создать самозаверяющий сертификат.
-    Связывает открытый ключ сертификата с субъектом-службой в Azure AD.
-    Сохраняет сертификат в хранилище сертификатов на локальном компьютере.
-    Предоставляет пользователю в сети доступ к закрытому ключу сертификата.
-    Перезапускает расширение NPS.

Если вы не намерены использовать собственные сертификаты (вместо самозаверяющих сертификатов, которые создает скрипт PowerShell), запустите скрипт PowerShell для завершения установки. Если установить расширение на нескольких серверах, каждый из них должен использовать собственный сертификат.

1. Запустите Windows PowerShell от имени администратора.
2. Перейдите в соответствующий каталог.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Запустите сценарий PowerShell, созданный установщиком.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. PowerShell запросит ваш идентификатор клиента. Введите GUID каталога, скопированный с портала Azure при выполнении предварительных требований. 
5. Войдите в Azure AD как администратор.
6. PowerShell отобразит сообщение об успешном выполнении после завершения сценария.  

Повторите эти действия на всех дополнительных серверах NPS, которые необходимо настроить для балансировки нагрузки. 

## <a name="configure-your-nps-extension"></a>Настройка расширения NPS

Этот раздел содержит рекомендации по проектированию и успешному развертыванию расширения NPS.

### <a name="configurations-limitations"></a>Ограничения конфигурации

- Расширение NPS для Azure MFA не включает в себя инструменты для переноса пользователей и настроек с сервера MFA в облако. По этой причине мы рекомендуем вам использовать расширение для новых развертываний, а не для существующего развертывания. При использовании расширения в существующем развертывании пользователям придется еще раз подтвердить свою личность, чтобы заполнить сведения для MFA в облаке.  
- Расширение NPS использует имя участника-пользователя из локального каталога Active Directory и по нему определяет пользователя MFA Azure, для которого будет выполняться дополнительная проверка подлинности. Расширение не может использовать другие идентификаторы, например альтернативное имя для входа или настраиваемые поля AD.  

### <a name="control-radius-clients-that-require-mfa"></a>Управление клиентами RADIUS, для которых требуется MFA

Когда вы включаете для клиента RADIUS поддержку MFA с помощью расширения NPS, все сеансы проверки подлинности для этого клиента могут выполняться только с использованием MFA. Если вы хотите включить MFA только для отдельных клиентов RADIUS, настройте два сервера NPS и установите расширение только на один из них. Затем настройте для клиентов RADIUS, которые должны использовать MFA, отправку запросов на сервер NPS, на котором установлено это расширение. Всех остальных клиентов RADIUS направьте на другой сервер NPS, для которого расширение не используется.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Подготовка к входу пользователей, не зарегистрированных для MFA

Если у вас есть пользователи, не зарегистрированные для MFA, вы можете выбрать действие, которое будет выполняться при попытке проверки подлинности. Для управления этим поведением используйте параметр реестра *REQUIRE_USER_MATCH*, расположенный в пути *HKLM\Software\Microsoft\AzureMFA*. Здесь есть только один параметр конфигурации:

| Ключ | Значение | значение по умолчанию |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | True или false | Не задано (эквивалентно значению True) |

Этот параметр определяет, что нужно делать при проверке подлинности пользователя, не зарегистрированного для MFA. Если этот ключ не существует, не задан или имеет значение True, то для незарегистрированного пользователя проверка MFA считается не пройденной. Если этот ключ имеет значение False, то для незарегистрированного пользователя проверка подлинности выполняется без MFA.

Вы можете создать этот ключ и присвоить ему значение False, если пользователи подключаются, но еще не зарегистрированы для Azure MFA. Однако такая настройка ключа разрешает вход всем пользователям, которые не зарегистрированы для MFA, поэтому этот ключ следует удалить до начала использования в рабочей среде.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Как проверить, правильно ли установлен сертификат клиента?

Найдите самозаверяющий сертификат, созданный установщиком в хранилище сертификатов, и убедитесь, что его закрытый ключ имеет разрешения, предоставленные пользователю **NETWORK SERVICE**. У этого сертификата имя субъекта-службы будет иметь значение **CN \<tenantid\>, а OU — Microsoft NPS Extension**.

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Как проверить, связан ли сертификат клиента с правильным клиентом в Azure Active Directory?

Откройте командную строку PowerShell и выполните следующие команды:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Эти команды выводят в сеанс PowerShell все сертификаты, благодаря которым ваш клиент связывается с экземпляром расширения NPS. Экспортируйте сертификат клиента в файл в кодировке Base-64 X.509 (CER) без закрытого ключа и сравните его со списком в PowerShell, чтобы найти соответствие.

Метки времени Valid-From и Valid-Until, которые имеют понятный для человека формат, позволят отфильтровать очевидные несоответствия, если команда возвращает более одного сертификата.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Почему запросы завершаются сбоем с ошибкой маркера ADAL?

Эта ошибка может быть вызвана одной из нескольких причин. Выполните следующие действия для устранения неполадок.

1. Перезагрузите сервер политики сети.
2. Убедитесь, что сертификат клиента установлен правильно.
3. Убедитесь, что сертификат связан с клиентом в Azure AD.
4. Убедитесь, что адрес https://login.windows.net/ доступен с сервера, на котором выполняется расширение.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Почему проверка подлинности завершается ошибкой, а в журнал HTTP заносится ошибка с сообщением о том, что пользователь не найден?

Убедитесь, что служба AD Connect работает, а нужный пользователь присутствует как в Windows Active Directory, так и в Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Почему в журналах отображаются ошибки подключения HTTP для всех попыток проверки подлинности?

Убедитесь, что адрес https://adnotifications.windowsazure.com доступен с сервера, на котором выполняется расширение NPS.

## <a name="next-steps"></a>Дальнейшие действия

Научитесь интегрировать MFA Azure с [Active Directory](multi-factor-authentication-get-started-server-dirint.md), [проверкой подлинности RADIUS](multi-factor-authentication-get-started-server-radius.md) и [проверкой подлинности LDAP](multi-factor-authentication-get-started-server-ldap.md).

