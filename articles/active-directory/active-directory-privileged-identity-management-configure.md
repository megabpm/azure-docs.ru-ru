---
title: "Настройка Azure AD Privileged Identity Management | Документация Майкрософт"
description: "В этой статье приводятся общие сведения об управлении привилегированными пользователями Azure AD и об использовании этой функции для повышения безопасности облака."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: 89174dad8fcd3bcceafd728feb2211926266720a
ms.contentlocale: ru-ru
ms.lasthandoff: 02/28/2017

---

# <a name="what-is-azure-ad-privileged-identity-management"></a>Что такое Azure AD Privileged Identity Management?
С помощью управления привилегированными пользователями в Azure Active Directory (AD) можно администрировать, контролировать и отслеживать доступ в пределах организации, в том числе доступ к ресурсам в Azure AD и остальным веб-службам Microsoft, таким как Office 365 или Microsoft Intune.  

> [!NOTE]
> Управление привилегированными пользователями доступно только в выпуске Azure Active Directory Premium P2. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).

Организациям необходимо свести к минимуму число пользователей, имеющих доступ к защищенным сведениям или ресурсам. Это снижает вероятность того, что такой доступ получит злоумышленник. Однако пользователям иногда требуется выполнять привилегированные операции в приложениях Azure, Office 365 или SaaS. Организации предоставляют пользователям привилегированный доступ в Azure AD, не отслеживая, как именно пользователи используют свои права администратора. Azure AD Privileged Identity Management помогает устранить этот риск.  

Управление привилегированными пользователями Azure AD помогает:  

* видеть, какие пользователи являются администраторами Azure AD;
* своевременно включать административный доступ по требованию к службам Microsoft Online Services, таким как Office 365 и Intune;
* получать отчеты по данным журнала доступа администраторов и изменениям в назначениях администраторов;
* получать оповещения о доступе к привилегированным ролям;

Azure AD Privileged Identity Management позволяет управлять встроенными ролями Azure AD в организации, включая следующие роли (но не ограничиваясь ими):  

* глобального администратора;
* администратора выставления счетов; 
* администратора служб;  
* администратора пользователей;
* администратора паролей.

## <a name="just-in-time-administrator-access"></a>Своевременный доступ к правам администраторов
Сложилось так, что пользователям можно назначать роль администратора на классическом портале Azure или в Windows PowerShell. В результате пользователь становится **постоянным администратором**, который всегда активен в назначенной роли. В управлении привилегированными пользователями Azure AD вводится понятие **разрешенный администратор**. Временные администраторы — это пользователи, которым привилегированный доступ требуется время от времени, а не каждый день. Эта роль будет неактивной, пока пользователю не потребуется доступ. При необходимости пользователь выполнит активацию и станет активным администратором на заранее определенный период времени.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Включение управления привилегированными пользователями для каталога
Чтобы воспользоваться управлением привилегированными пользователями Azure AD, зайти на [портал Azure](https://portal.azure.com/).

> [!NOTE]
> Чтобы включить Azure AD Privileged Identity Management для каталога, необходимо быть глобальным администратором с учетной записью организации (например, @yourdomain.com), а не учетной записью Майкрософт (например, @outlook.com).

1. Войдите на [портал Azure](https://portal.azure.com/) как глобальный администратор нужного каталога.
2. Если в организации имеется несколько каталогов, выберите свое имя пользователя в правом верхнем углу на портале Azure. Затем выберите каталог, в котором будет использоваться управление привилегированными пользователями Azure AD.
3. Выберите **Другие службы** и в текстовое поле "Фильтр" введите **Azure AD Privileged Identity Management**.
4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**. Откроется приложение Privileged Identity Management.

Если вы первый пользователь, использующий управление привилегированными пользователями Azure AD в каталоге, запустится [мастер защиты](active-directory-privileged-identity-management-security-wizard.md) , который поможет вам выполнить первое назначение. После этого вы автоматически станете первым **администратором безопасности** и **администратором привилегированных ролей** каталога.

Управлять правами доступа других администраторов может только администратор привилегированных ролей. Вы можете [предоставить другим пользователям возможность управлять PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Панель мониторинга Azure AD Privileged Identity Management
Диспетчер привилегированных удостоверений Azure AD предоставляет панель мониторинга, на которой отображается такая важная информация, как:

* оповещения, показывающие, как можно повысить уровень безопасности;
* количество пользователей, которые назначены в каждую привилегированную роль;  
* количество временных и постоянных администраторов;
* регулярные проверки доступа.

![Снимок экрана: панель мониторинга управления привилегированными пользователями (PIM)][2]

## <a name="privileged-role-management"></a>Управление привилегированными ролями
С помощью управления привилегированными пользователями Azure AD можно управлять учетными записями администраторов, добавляя или удаляя постоянных и временных администраторов для каждой роли.

![Снимок экрана: добавление и удаление администраторов PIM][3]

## <a name="configure-the-role-activation-settings"></a>Настройка параметров активации ролей
[Параметры ролей](active-directory-privileged-identity-management-how-to-change-default-settings.md) позволяют настраивать свойства активации временных ролей, в том числе:

* длительность периода активации роли;
* уведомления об активации роли;
* данные, которые нужно указать пользователю во время активации роли.  

![Снимок экрана: параметры PIM — активация администратора][4]

Обратите внимание, что на изображении кнопки для **Multi-Factor Authentication** неактивны. Для некоторых ролей с высоким уровнем привилегий требуется использование MFA для дополнительной защиты.

## <a name="role-activation"></a>Активация роли
Чтобы [активировать роль](active-directory-privileged-identity-management-how-to-activate-role.md), временный администратор отправляет запрос на активацию роли с привязкой по времени. Запрос на активацию можно отправить, используя параметр **Активировать мою роль** в Azure AD Privileged Identity Management.

Администратору, которому нужно активировать роль, необходимо инициализировать управление привилегированными пользователями Azure AD на портале Azure.

Активацию роли можно настраивать. В параметрах управления привилегированными пользователями можно определить продолжительность активации, а также сведения, которые администратор должен указать для активации роли.

![Снимок экрана: запрос администратора — активация роли][5]

## <a name="review-role-activity"></a>Действие проверки роли
Отследить, как сотрудники и администраторы используют привилегированные роли, можно двумя способами. Первый способ — с помощью [журнала аудита](active-directory-privileged-identity-management-how-to-use-audit-log.md). В журналах аудита отслеживаются изменения в назначениях привилегированных ролей и журнале активации ролей.

![Снимок экрана: журнал активации PIM][6]

Второй способ — настроить регулярные [проверки доступа](active-directory-privileged-identity-management-how-to-start-security-review.md). Эти проверки может выполнять назначенный проверяющий (например, руководитель группы); также сотрудники могут проверять сами себя. Это лучший способ отследить, кому по-прежнему требуется доступ, а кому уже нет.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM по истечении срока действия подписки
Перед выпуском общедоступной версии технология Azure AD PIM была доступна в предварительной версии. Проверки лицензии клиента для использования предварительной версии Azure AD PIM не выполнялись.  Теперь, когда вышла общедоступная версия Azure AD PIM, для использования PIM после декабря 2016 г. необходимо наличие в клиенте пробной или платной подписки.  Если ваша организация не приобрела Azure AD Premium P2 или срок действия подписки истекает, технология Azure AD PIM больше не будет доступна в вашем клиенте.  Дополнительные сведения см. на странице [требований к подписке для Azure AD PIM](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

