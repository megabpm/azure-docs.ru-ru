---
title: "Настройка утверждений, выпущенных в маркере SAML для предварительно интегрированных приложений в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить утверждения, выпущенные в маркере SAML для предварительно интегрированных приложений в Azure Active Directory"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Настройка утверждений, выпущенных в маркере SAML для предварительно интегрированных приложений в Azure Active Directory
На сегодняшний день Azure Active Directory поддерживает тысячи предварительно интегрированных приложений в коллекции приложений Azure AD, включая более 150 приложений, поддерживающих единый вход по протоколу SAML 2.0. Когда пользователь проходит проверку подлинности для приложения в Azure AD с помощью SAML, Azure AD отправляет маркер в приложение (через перенаправление HTTP 302). Затем приложение проверяет и использует маркер для входа пользователя вместо запроса имени пользователя и пароля. Эти маркеры SAML содержат элементы информации о пользователе, которые называются "утверждениями".

С точки зрения удостоверений "утверждение" представляет собой информацию, предложенную поставщиком удостоверений о пользователе в составе маркера, выпущенного для этого пользователя. В [маркере SAML](http://en.wikipedia.org/wiki/SAML_2.0)эти данные обычно содержит оператор атрибута SAML, а уникальный идентификатор пользователя, как правило, представлен в субъекте SAML.

По умолчанию Azure AD выпускает маркер SAML для приложения, которое содержит утверждение NameIdentifier, с указанием имени пользователя в Azure AD. Это значение обеспечивает уникальную идентификацию пользователя. Маркер SAML включает также дополнительные утверждения, содержащие адрес электронной почты, имя и фамилию пользователя.

Чтобы просмотреть или изменить утверждения, выпущенные в маркере SAML для приложения, откройте запись приложения на классическом портале Azure и откройте вкладку **Атрибуты** под этим приложением.

![Вкладка "Атрибуты"][1]

Изменение утверждений, выданных в маркере SAML, может потребоваться по двум основным причинам:
* Приложение требует другого набора URI утверждений или значений утверждений. 
* Приложение развернуто таким образом, что утверждение NameIdentifier должно содержать данные, отличные от имени пользователя (также называемого именем участника-пользователя), которое хранится в Azure Active Directory. 

Вы можете изменять в утверждении любые значения, используемые по умолчанию. Выберите значок в форме карандаша, который появляется справа при наведении указателя мыши на одну из строк в таблице атрибутов маркера SAML. Значок **X** позволяет удалить утверждения (кроме утверждения NameIdentifier), а кнопка **Добавить атрибут пользователя** — добавить новые утверждения.

## <a name="editing-the-nameidentifier-claim"></a>Редактирование утверждения NameIdentifier
Чтобы решить проблему, связанную с тем, что приложение было развернуто с указанием другого имени пользователя, щелкните значок карандаша рядом с утверждением NameIdentifier. Это действие выводит диалоговое окно с несколькими различными параметрами:

![Изменение атрибута пользователя][2]

В меню **Значение атрибута** выберите пункт **user.mail**, чтобы присвоить утверждению NameIdentifier адрес электронной почты пользователя в каталоге. Или выберите **user.onpremisessamaccountname**, чтобы присвоить SAM пользователя имя учетной записи, синхронизированное из локального каталога Azure AD.

Можно также использовать специальную функцию ExtractMailPrefix() для удаления суффикса домена из адреса электронной почты или имени участника-пользователя. В таком случает будет передаваться только первая часть имения пользователя (например, joe_smith вместо joe_smith@contoso.com).

![Изменение атрибута пользователя][3]

## <a name="adding-claims"></a>Добавление утверждений
При добавлении утверждения можно указать имя атрибута (которое не обязательно соответствует шаблону URI согласно спецификации SAML). Задайте значение любого атрибута пользователя, который хранится в каталоге.

![Добавление атрибута пользователя][4]

Например, необходимо отправить имя подразделения организации, к которому принадлежит пользователь (возьмем отдел продаж), в виде утверждения. Вы можете ввести любое значение утверждения, ожидаемое приложением, а затем выбрать значение **user.department**.

Если для заданного пользователя нет сохраненного значения выбранного атрибута, это утверждение не будет выпущено в маркере.

**Примечание**. Значения **user.onpremisesecurityidentifier** и **user.onpremisesamaccountname** поддерживаются только при синхронизации данных пользователя из локального каталога Active Directory с помощью [инструмента Azure AD Connect](../active-directory-aadconnect.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Указатель статьей по управлению приложениями в Azure Active Directory](../active-directory-apps-index.md)
* [Настройка единого входа для приложений, которых нет в коллекции приложений Azure Active Directory](../active-directory-saas-custom-apps.md)
* [Устранение неполадок единого входа на основе SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
