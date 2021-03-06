---
title: "Условный доступ Azure для приложений SaaS | Документация Майкрософт"
description: "Функция условного доступа в Azure AD позволяет настроить правила доступа многофакторной проверки подлинности для каждого приложения и блокировать доступ для пользователей не в доверенной сети. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4720d168f5ecd3a5e39cdcad812efb52755fb59a
ms.contentlocale: ru-ru
ms.lasthandoff: 12/28/2016


---
# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Приступая к работе с условным доступом Azure Active Directory
Условный доступ Azure Active Directory для приложений [SaaS](https://azure.microsoft.com/overview/what-is-saas/) и подключенных приложений Azure AD позволяет настраивать условный доступ на основе группы, расположения и конфиденциальности приложения. 

Используя условный доступ на основе конфиденциальности приложений, можно настроить правила многофакторной идентификации (MFA) для каждого приложения отдельно. Это дает возможность блокировать пользователей, которые не находятся в доверенной сети. Правила MFA можно применять ко всем пользователям, которые имеют доступ к приложению, или только к пользователям, которые входят в определенные группы безопасности.  Пользователи могут быть исключены из требования MFA, если они получают доступ к приложению с IP-адреса в сети организации.

Эти возможности будут доступны клиентам, которые приобрели лицензию Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Предварительные условия для сценария
* Лицензия на Azure Active Directory Premium.
* Федеративный или управляемый клиент Azure Active Directory
* Федеративным клиентам требуется многофакторная проверка подлинности.

## <a name="configure-per-application-access-rules"></a>Настройка правил доступа для каждого приложения
В этом разделе описывается настройка правил доступа для каждого приложения.

1. Войдите на классический портал Azure, используя учетную запись глобального администратора для Azure AD.
2. На панели слева выберите **Active Directory**.
3. На вкладке «Каталог» выберите требуемый каталог.
4. Выберите вкладку **Приложения** .
5. Выберите приложение, для которого будет установлено правило.
6. Перейдите на вкладку **Настройка** .
7. Прокрутите экран вниз до раздела правил доступа. Выберите нужное правило доступа.
8. Укажите пользователей, к которым будет применяться правило.
9. Включите политику, выбрав для параметра **Включено**значение «Вкл.».

## <a name="understanding-access-rules"></a>Основные данные о правилах доступа
Этот раздел содержит подробное описание правил доступа, которые поддерживает условный доступ Azure для приложений.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Определение пользователей, к которым применяются правила доступа
По умолчанию политика применяется ко всем пользователям, имеющим доступ к приложению. Однако политика также может применяться только к пользователям, которые входят в определенную группу безопасности. Кнопка **Добавить группу** используется для выбора одной или нескольких групп, к которым будут применяться правила доступа, в диалоговом окне выбора групп. В этом диалоговом окне можно также удалить выбранные группы. Правила доступа, выбранные для применения к группам, будут действовать только для пользователей, входящих в одну из указанных групп безопасности.

Группы безопасности можно также явно исключить из политики, установив флажок **Кроме** и указав одну или несколько групп. К пользователям, входящим в группу из списка **Кроме** , не будет применяться требование многофакторной идентификации, даже если они входят в группу, к которой применяется правило доступа.
Правило доступа, приведенное ниже, будет требовать использования многофакторной идентификации при доступе к приложению от всех пользователей в группе "Руководители".

![Настройка правил условного доступа с использованием многофакторной проверки подлинности](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Правила условного доступа с многофакторной проверкой подлинности
Если пользователь был настроен с использованием функции индивидуальной многофакторной проверки подлинности, этот параметр для пользователя будет сочетаться с правилами многофакторной проверки подлинности приложения. Это означает, что пользователь, для которого была настроена индивидуальная многофакторная проверка подлинности, должен будет проходить многофакторную проверку подлинности, даже если он был исключен из правил многофакторной проверки подлинности приложения. Узнайте больше о многофакторной проверке подлинности и пользовательских параметрах.

### <a name="access-rule-options"></a>Варианты правил доступа
Поддерживаются следующие варианты.

* **Требовать многофакторную проверку подлинности**: пользователям, к которым применяются правила доступа, потребуется пройти многофакторную идентификацию. Только так они смогут получить доступ к приложению, к которому применяется политика.
* **Требовать многофакторную проверку подлинности, если не на работе**: пользователю с доверенным IP-адресом не нужно проходить многофакторную идентификацию. Диапазоны надежных IP-адресов можно задать на странице параметров многофакторной проверки подлинности.
* **Блокировать доступ, если не на работе**: пользователь с недоверенным IP-адресом будет заблокирован. Диапазоны надежных IP-адресов можно задать на странице параметров многофакторной проверки подлинности.

### <a name="setting-rule-status"></a>Настройка состояния правила
Параметр состояния правила доступа можно включать и выключать. При выключении правил доступа требование многофакторной идентификации применяться не будет.

### <a name="access-rule-evaluation"></a>Оценка правил доступа
Правила доступа оцениваются, когда пользователь обращается к федеративному приложению, использующему OAuth 2.0, OpenID Connect, SAML или WS-Federation. Кроме того, правила доступа оцениваются, когда для получения маркера доступа OAuth 2.0 и OpenID Connect используют маркер обновления. Если при использовании маркера обновления оценка политики завершается сбоем, возвращается ошибка **invalid_grant**, которая указывает, что пользователь должен пройти повторную аутентификацию в клиенте.

### <a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Настройка служб федерации для обеспечения многофакторной проверки подлинности
Для федеративных клиентов MFA могут выполнять Azure Active Directory или локальный сервер AD FS.

По умолчанию многофакторная проверка подлинности осуществляется на странице, размещенной в Azure Active Directory. Чтобы настроить локальную MFA, воспользуйтесь модулем Azure AD для Windows PowerShell и в Azure Active Directory задайте для свойства **–SupportsMFA** значение **True**.

В следующем примере показано, как включить локальную многофакторную проверку подлинности, используя [командлет Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) в клиенте contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Установив этот флажок, также следует настроить выполнение многофакторной проверки подлинности для экземпляра федеративного клиента AD FS. Выполните указания по [локальному развертыванию многофакторной идентификации Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Связанные статьи
* [Защита доступа к Office 365 и другим приложениям, подключенным к Azure Active Directory](active-directory-conditional-access.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)


