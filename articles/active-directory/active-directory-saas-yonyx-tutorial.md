---
title: "Руководство по интеграции Azure Active Directory с Yonyx Interactive Guides | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Yonyx Interactive Guides."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 0d2ac97c7f8e42f1e6b847e338d0676592054d93
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Руководство по интеграции Azure Active Directory с Yonyx Interactive Guides
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Yonyx Interactive Guides.

Интеграция Azure AD с приложением Yonyx Interactive Guides обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Yonyx Interactive Guides.
* Вы можете включить автоматический вход пользователей в Yonyx Interactive Guides (единый вход) с помощью учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Yonyx Interactive Guides, вам потребуется:

* подписка Azure AD;
* подписка на Yonyx Interactive Guides с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Yonyx Interactive Guides из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Добавление Yonyx Interactive Guides из коллекции
Чтобы настроить интеграцию Yonyx Interactive Guides с Azure AD, необходимо добавить Yonyx Interactive Guides из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Yonyx Interactive Guides из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Yonyx Interactive Guides**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_01.png)
7. В области результатов выберите **Yonyx Interactive Guides** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Yonyx Interactive Guides с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Yonyx Interactive Guides соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Yonyx Interactive Guides.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Yonyx Interactive Guides.

Чтобы настроить и проверить единый вход Azure AD в Yonyx Interactive Guides, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Yonyx Interactive Guides](#creating-a-yonyx-interactive-guides-test-user)**. Требуется для создания в Yonyx Interactive Guides пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Yonyx Interactive Guides.

**Чтобы настроить единый вход Azure AD в Yonyx Interactive Guides, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Yonyx Interactive Guides** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Yonyx Interactive Guides?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_03.png)
3. На странице диалогового окна **Настроить параметры приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_04.png)
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`. 
  2. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.yonyx.com`.
  3. Нажмите кнопку **Далее**.
   
    >[!NOTE]
    > Обратите внимание, что вместо этих значений нужно указать фактический URL-адрес для входа и идентификатор. Чтобы получить эти значения, обратитесь в службу поддержки Yonyx Interactive Guides по адресу <mailto:support@yonyx.com>. 
    > 
4. На странице **Настройка единого входа в Yonyx Interactive Guides** щелкните **Скачать сертификат** и сохраните полученный файл на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_05.png)
5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Yonyx Interactive Guides по адресу <mailto:support@yonyx.com> и предоставьте следующие сведения:
  * скачанный **сертификат**;
  * **URL-адрес издателя**
  * **URL-адрес службы единого входа**;
  * **URL-адрес службы единого выхода**
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Единый вход в Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_05.png) 
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_06.png) 
 1. В текстовом поле **Имя** введите **Britta**.   
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-yonyx-tutorial/create_aaduser_08.png) 
 1. Запишите значение поля **Новый пароль**.
 2. Нажмите **Завершено**.   

### <a name="create-a-yonyx-interactive-guides-test-user"></a>Создание тестового пользователя Yonyx Interactive Guides
Цель этого раздела — создать в приложении Yonyx Interactive Guides пользователя с именем Britta Simon. Приложение Yonyx Interactive Guides поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Yonyx Interactive Guides (если он еще не создан).

>[!NOTE]
>Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки Yonyx Interactive Guides по адресу <mailto:support@yonyx.com>. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Yonyx Interactive Guides, чтобы этот пользователь мог использовать единый вход Azure.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Yonyx Interactive Guides, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Yonyx Interactive Guides**.
   
    ![Настройка единого входа](./media/active-directory-saas-yonyx-tutorial/tutorial_yonyx_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Yonyx Interactive Guides на панели доступа, вы автоматически войдете в приложение Yonyx Interactive Guides.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-yonyx-tutorial/tutorial_general_205.png

