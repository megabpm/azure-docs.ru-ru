---
title: "Руководство по лицензированию службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Для службы совместной работы Azure Active Directory B2B требуются платные лицензии Azure AD. Тип лицензии зависит от функций, которые необходимы пользователям службы совместной работы B2B."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 4e620f3d76caa25ac0e5afb134f37ffe263935f0
ms.contentlocale: ru-ru
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Руководство по лицензированию службы совместной работы Azure Active Directory B2B

Служба совместной работы Azure Active Directory (Azure AD) B2B расширяет выбранный набор существующих функций Azure AD для гостевых пользователей, приглашенных в клиент Azure AD. Поэтому к гостевым пользователям службы совместной работы Azure AD B2B будут применяться правила лицензирования Azure AD с соответствующим разделением на уровни лицензий ("Бесплатный", "Базовый" и "Премиум P1 или P2"), как показано на этой странице: https://azure.microsoft.com/pricing/details/active-directory/.

За приглашение пользователей B2B и их назначение приложениям в Azure AD плата не взимается. Кроме того, до 10 приложений на каждого гостевого пользователя и 3 базовых отчета также являются бесплатными для пользователей B2B, так как они относятся к уровню "Бесплатный" службы Azure AD.
На любые платные функции Azure AD, расширенные для пользователей B2B с помощью службы совместной работы B2B, необходимо приобрести лицензии Azure AD (уровня "Базовый", "Премиум P1" или "Премиум P2" в зависимости от функций, которые будут использоваться). С каждой платной лицензией приглашающий клиент получает права на 5 пользователей B2B. То есть каждая платная лицензия Azure AD, которая предоставляет права одному сотруднику в клиенте, теперь также предоставляет права на использование аналогичных платных функций Azure AD для 5 пользователей B2B, приглашаемых в клиент.

## <a name="licensing-examples"></a>Примеры лицензирования
- Заказчик хочет пригласить в свой клиент Azure AD 100 пользователей B2B и будет использовать управление доступом и подготовку на основе групп для всех пользователей, но для 50 из них также потребуется Многофакторная идентификация и условный доступ. В данном случае заказчик должен приобрести 10 лицензий Azure AD уровня "Базовый" и 10 лицензий Azure AD уровня "Премиум P1", чтобы правильно охватить всех своих пользователей B2B. Аналогично, если приглашающий клиент планирует использовать в отношении пользователей B2B функции защиты идентификации, то он должен иметь достаточно лицензий Azure AD уровня "Премиум P2", чтобы охватить всех этих пользователей в соотношении 5:1.
- У заказчика 10 сотрудников, которые в данный момент имеют лицензии Azure AD уровня "Премиум P1". Теперь им нужно пригласить 60 пользователей B2B, в отношении которых необходимо применить Многофакторную идентификацию (MFA). Согласно правилу лицензирования с соотношением 5:1 заказчику требуется не менее 12 лицензий Azure AD уровня "Премиум P1", чтобы охватить всех 60 пользователей службы совместной работы B2B. Поскольку у заказчика уже есть 10 лицензий уровня "Премиум P1" для 10 сотрудников, они уже имеют право пригласить 50 пользователей B2B с функциями уровня "Премиум P1", такими как MFA. Таким образом, в данном примере они должны дополнительно приобрести 2 лицензии уровня "Премиум P1", чтобы покрыть оставшихся 10 пользователей службы совместной работы B2B.

> [!NOTE]
> Нет необходимости назначать лицензии пользователям B2B, чтобы добавить эти права пользователей службы совместной работы B2B.

Заказчик, владеющий приглашающим клиентом, должен быть тем, кто определяет, скольким пользователям службы совместной работы B2B требуются платные возможности Azure AD. И в зависимости от принадлежности к уровню "Базовый", "Премиум P1" или "Премиум P2" заказчик должен иметь достаточное количество соответствующих платных лицензий Azure AD, чтобы охватить своих пользователей службы совместной работы B2B в соотношении 5:1. Если компании требуются дополнительные права для пользователей службы совместной работы B2B, то она должна приобрести необходимые платные лицензии Azure AD.

## <a name="additional-licensing-details"></a>Дополнительные сведения о лицензировании
- В службе совместной работы B2B разным пользователям можно предоставлять разные возможности, исходя из существующей модели выпусков Azure AD.
- Каждая платная лицензия Azure AD включает в себя права для 5 пользователей службы совместной работы B2B (модель 5:1).
- Фактически нет необходимости назначать лицензии учетным записям пользователей B2B. Будет автоматически выполнено вычисление и создан отчет.
- Если в клиенте нет платной лицензии Azure AD, то каждый приглашенный пользователь получит права, предлагаемые Azure AD в рамках выпуска "Бесплатный".
- Если у пользователя службы совместной работы B2B есть платная лицензия Azure AD как у сотрудника своей организации, то приглашающий клиент не потратит на него одну из таких лицензий.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Расширенное обсуждение. Каковы особенности лицензирования при добавлении пользователей из организации, входящей в конгломерат, в качестве "участников" с использованием ваших API?
Гостевой пользователь B2B — это тот, кто приглашен из партнерской организации работать с главной организацией. Обычно любой другой вариант не считается B2B, даже если используются функции B2B. Рассмотрим две конкретные ситуации.

1. Если главная организация приглашает сотрудника с использованием адреса потребителя.
  1. Это не соответствует нашим политикам лицензирования и в настоящее время не рекомендуется.

2. Если главная организация добавляет пользователя из другой организации конгломерата.
  1. В таком случае пользователь приглашается с помощью API B2B, но это не обычный сценарий B2B. В идеале эти организации должны пригласить пользователей другой организации как участников (наш API это позволяет). В этом случае этим участникам для получения доступа к ресурсам приглашающей организации должны быть назначены лицензии.

  2. Некоторые организации могут захотеть добавить пользователей других организаций в качестве "гостей" в соответствии с политикой. Есть два варианта:
      * Организация конгломерата уже использует Azure AD и приглашенные пользователи лицензированы в другой организации. В этом случае для приглашенных пользователей необязательно следовать формуле 1:5, изложенной ранее в этом документе. 

      * Организация конгломерата не использует Azure AD или не имеет достаточно лицензий. В таком случае следуйте формуле 1:5, изложенной ранее в этом документе.


## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [API службы совместной работы Azure Active Directory B2B и настройка](active-directory-b2b-api.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Добавление пользователей службы совместной работы B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

