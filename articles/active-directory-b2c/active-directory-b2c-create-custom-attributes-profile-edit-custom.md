---
title: "Azure Active Directory B2C. Добавление собственных атрибутов в пользовательские политики и их использование при изменении профиля | Документация Майкрософт"
description: "Пошаговое руководство по использованию свойств расширения и настраиваемых атрибутов, а также по их добавлению в пользовательский интерфейс"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 83748140c7b92b95a648ae3ecf78f22e2393780b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017

---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C. Создание и использование настраиваемых атрибутов в пользовательской политике изменения профиля

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье вы создадите настраиваемый атрибут в каталоге Azure AD B2C и используете его в качестве пользовательского утверждения при изменении профиля.

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Использование настраиваемых атрибутов для сбора данных о клиентах в Azure Active Directory B2C с помощью пользовательских политик
Каталог Azure Active Directory (Azure AD) B2C поставляется со встроенным набором атрибутов: Given Name, Surname, City, Postal Code, userPrincipalName и т. д.  Вы часто будете создавать собственные атрибуты.  Например:
* Клиентскому приложению требуется сохранить атрибут LoyaltyNumber.
* Поставщик удостоверений имеет уникальный идентификатор пользователя, который необходимо сохранить, например uniqueUserGUID.
* В настраиваемом пути взаимодействия пользователя нужно сохранить состояние, например migrationStatus.

С помощью Azure AD B2C можно расширить набор атрибутов, хранящихся в каждой учетной записи пользователя. Можно также читать и записывать эти атрибуты с помощью [API Graph Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

[!NOTE]
Мы называем настраиваемый атрибут или свойство расширения функциями каталога Azure AD B2C.  Свойства расширения расширяют схему объектов пользователей в каталоге.  Чтобы использовать настраиваемый атрибут в виде настраиваемого утверждения, определите его в политике в свойстве `ClaimsSchema`.

[!NOTE]
Свойства расширения можно зарегистрировать только в объекте приложения, даже если они содержат данные для пользователя. Свойство присоединяется к приложению. Объекту приложения нужно предоставить доступ на запись для регистрации свойства расширения. В один объект можно записать не более 100 значений расширений (для всех типов и приложений). Свойство добавляется к целевому каталогу и сразу становится доступным в клиенте каталога Azure AD B2C.
При удалении приложения эти свойства расширения и все данные, содержащиеся в них, для всех пользователей также удаляются. Если свойство расширения удаляется приложением, оно удаляется в объекте целевого каталога со всеми данными.

[!NOTE]
Свойства расширения существуют только в контексте зарегистрированного приложения в клиенте. Идентификатор объекта приложения должен содержаться в техническом профиле, использующем его.

[!NOTE]
Каталог Azure AD B2C обычно включает приложение веб-API с именем `b2c-extensions-app`.  Это приложение главным образом используется встроенными политиками B2C для пользовательских утверждений, созданных с помощью портала Azure.  Мы рекомендуем регистрировать расширения для пользовательских политик B2C с помощью этого приложения только для опытных пользователей.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Создание приложения для хранения свойств расширения

1. Откройте сеанс браузера, перейдите к [порталу Azure](https://portal.azure.com) и выполните вход с использованием учетных данных администратора каталога B2C, который требуется настроить.
1. В области навигации слева щелкните `Azure Active Directory`. Чтобы найти эту службу, щелкните > More Services (> Больше служб).
1. Щелкните `App registrations` и `New application registration`.
1. Укажите следующие рекомендуемые записи:
  * Укажите имя для веб-приложения: `WebApp-GraphAPI-DirectoryExtensions`.
  * Тип приложения: веб-приложение или API.
  * URL-адрес входа: https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions.
1. Выберите `Create`. В области `notifications` появится уведомление об успешной операции.
1. Выберите созданное веб-приложение: `WebApp-GraphAPI-DirectoryExtensions`.
1. Выберите "Параметры": `Required permissions`.
1. Выберите API `Windows Active Directory`.
1. В разделе "Разрешения приложений" установите флажок : `Read and write directory data` и `Save`.
1. Выберите `Grant permissions` и подтвердите свой выбор (`Yes`).
1. Скопируйте в буфер обмена и сохраните следующие идентификаторы из раздела "WebApp-GraphAPI-DirectoryExtensions > Параметры > Свойства".
*  `Application ID`. Пример: `103ee0e6-f92d-4183-b576-8c3739027780`
* `Object ID`. Пример: `80d8296a-da0a-49ee-b6ab-fd232aa45201`

## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Изменение пользовательской политики для добавления `ApplicationObjectId`

Для каждого технического профиля, который будет считывать или записывать атрибуты расширения, необходимо добавить элемент `<Metadata>` с двумя элементами: ApplicationObjectId и ClientId, полученными на предыдущих шагах.

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```
[!NOTE]
<TechnicalProfile Id="AAD-Common"> являет общим, так как его элементы повторно используются во всех технических профилях Azure Active Directory с элементами:
```
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
```
[!NOTE]
При первой записи TechnicalProfile в созданное свойство расширения может произойти одноразовая ошибка, так как свойство создается, если оно не найдено.  .*  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Использование нового свойства расширения или настраиваемого атрибута в пути взаимодействия пользователя


1. Откройте файл проверяющей стороны, в котором представлено описание пути взаимодействия пользователя с политикой изменения.  Сначала мы рекомендуем скачать уже настроенную версию файла RP-PolicyEdit непосредственно из раздела пользовательской политики Azure B2C на портале Azure.  Вы также можете открыть XML-файл из папки хранилища.
2. Добавьте пользовательское утверждение `loyaltyId`.  После добавления пользовательского утверждения в элемент `<RelyingParty>` оно будет передано как параметр в UserJourney TechnicalProfiles и включено в токен для приложения.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Добавьте определение утверждений в файл политики расширения `TrustFrameworkExtensions.xml` в элементе ``<ClaimsSchema>``, как показано ниже.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Добавьте это же определение утверждения в базовый файл политики `TrustFrameworkBase.xml`.  
Примечание. Обычно не нужно добавлять определение `ClaimType` в базовый файл и файл расширения. Но так как на следующих шагах extension_loyaltyId будет добавлен в TechnicalProfiles в базовом файле, проверяющий политики отклонит отправку базового файла без этого определения.

Примечание. Отследите выполнение пути взаимодействия пользователя ProfileEdit в файле TrustFrameworkBase.xml.  Найдите путь взаимодействия пользователя с тем же именем в редакторе и обратите внимание, что на шаге 5 оркестрации вызывается TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Найдите и проверьте этот TechnicalProfile, чтобы ознакомиться с рабочим процессом.

5. Добавьте loyaltyId в качестве входного и выходного утверждения в техническом профиле SelfAsserted-ProfileUpdate.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Добавьте утверждение в техническом профиле AAD-UserWriteProfileUsingObjectId, чтобы сохранить значение утверждения в свойство расширения для текущего пользователя в каталоге.

```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Добавьте утверждение в технический профиль AAD-UserReadUsingObjectId, чтобы считывать значение атрибута расширения при каждом входе пользователя в систему.
Примечание. До сих пор TechnicalProfiles изменялся только при работе с локальными учетными записями.  Если новый атрибут нужно использовать для учетной записи социальной сети или федеративной учетной записи, нужно изменить другой набор технических профилей. Ознакомьтесь с разделом "Дальнейшие действия".

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```

[!IMPORTANT]
Элемент IncludeTechnicalProfile выше добавляет все элементы AAD-Common в этот технический профиль.

## <a name="test-the-custom-policy-using-run-now"></a>Тестирование настраиваемой политики с помощью параметра "Запустить сейчас"

     1. Open the **Azure AD B2C Blade** and navigate to **Identity Experience Framework > Custom policies**.
     2. Select the custom policy that you uploaded, and click the **Run now** button.
     3. You should be able to sign up using an email address.

Идентификатор токена, отправляемый в приложение, будет включать новое свойство расширения в виде настраиваемого утверждения, перед которым будет указано extension_loyaltyId. Ознакомьтесь с примером ниже.

```
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Дальнейшие действия

Добавьте новое утверждение в рабочий процесс входа с использованием учетных записей социальных сетей, изменив технические профили, указанные ниже. Эти два технических профиля используются при входе с использованием учетной записи социальной сети или федеративной учетной записи для записи и чтения данных пользователя с применением alternativeSecurityId в качестве указателя объекта пользователя.
```
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```


## <a name="reference"></a>Справочные материалы

* **Технический профиль** — это элемент, который можно считать *функцией*, определяющей имя конечной точки, ее метаданные, протокол, а также предоставляющей сведения об обмене утверждениями, который должна выполнять инфраструктура процедур идентификации.  При вызове этой *функции* на шаге оркестрации или из другого технического профиля вызывающий объект указывает InputClaims и OutputClaims в качестве параметров.


* Полный список свойств расширения см. в статье [Расширения схемы каталогов | Общие понятия API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). 
[!NOTE]
Расширения атрибутов в Graph API именуются по соглашению `extension_ApplicationObjectID_attributename`. В пользовательских политиках атрибуты расширений указываются в формате extension_attributename, тем самым исключая ApplicationObjectId в XML.

