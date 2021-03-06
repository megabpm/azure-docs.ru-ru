---
title: "Вопросы безопасности для прокси приложения Azure AD | Документы Майкрософт"
description: "В этой статье рассматриваются вопросы безопасности при использовании прокси приложения Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 52704b3259ddad85db12b8bb81c4992ece2f37a4
ms.lasthandoff: 04/21/2017


---

# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Вопросы безопасности при удаленном доступе к приложениям через прокси приложения Azure AD

В этой статье описывается, каким образом прокси приложения Azure Active Directory (Azure AD) обеспечивает безопасную публикацию приложений и удаленный доступ к ним.

На следующей схеме показано, как Azure AD обеспечивает безопасный удаленный доступ к локальным приложениям.

 ![Схема безопасного удаленного доступа через прокси приложения Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

Прокси приложения Azure AD предоставляет следующие преимущества безопасности.

**Доступ с проверкой подлинности.** Только подключения, прошедшие проверку подлинности, могут получить доступ к сети.

* Прокси приложения Azure AD во всех случаях использует для проверки подлинности службу маркеров безопасности Azure AD.  Предварительная проверка подлинности организована так, что она автоматически блокирует значительное количество анонимных атак. Доступ к внутреннему приложению получают только удостоверения, прошедшие проверку подлинности.

**Условный доступ.** Примените расширенные параметры политики, прежде чем устанавливать подключения к сети.

* Благодаря [условному доступу](active-directory-conditional-access-azuread-connected-apps.md) можно определить ограничения в отношении трафика, который может проходить к внутренним приложениям, на основе расположения, надежности проверки подлинности и профиля риска пользователя.

**Завершение трафика.** Весь трафик завершается в облаке.

* Прокси приложения Azure AD работает как обратный прокси-сервер, поэтому в этой службе завершается весь трафик, направленный к внутренним приложениям. Сеанс возобновляется только для внутренних серверов, то есть эти внутренние серверы не получают трафик HTTP напрямую. Таким образом, вы можете легко устранить целевые атаки.

**Только исходящий доступ.** Для корпоративной сети не требуется открывать входящие подключения.

* Соединители Azure AD поддерживают исходящие подключения к службе прокси приложения Azure AD, поэтому нет необходимости открывать порты брандмауэра для входящих подключений. Для традиционных прокси требовалась сеть периметра (другие названия — *DMZ*, *демилитаризованная зона* и *промежуточная подсеть*) и разрешать на границе сети доступ для подключений без проверки подлинности. Это требовало дополнительных расходов на брандмауэры веб-приложения (WAF), которые анализируют трафик и защищают сетевую среду. Благодаря прокси приложения можно отказаться от сети периметра, так как теперь все подключения являются исходящими и передаются по защищенному каналу.

**Аналитика безопасности и аналитика на основе машинного обучения.** Используйте самые современные меры безопасности.

* Для [защиты идентификации Azure AD](active-directory-identityprotection.md) с аналитикой на основе машинного обучения используются данные из подразделения Digital Crimes Unit и Microsoft Security Response Center. Вместе мы в упреждающем режиме определяем скомпрометированные учетные записи и обеспечиваем защиту в реальном времени от входов, представляющих высокий уровень риска. Мы учитываем множество факторов, таких как доступ с зараженных устройств, из нетипичных и сомнительных расположений, а также через анонимизирующие сети.
* Многие из этих отчетов и событий уже сейчас можно использовать в API для интеграции с системами управления сведениями о безопасности и событиями (SIEM).

**Удаленный доступ как услуга.** Вам больше не нужно беспокоиться о поддержке локальных серверов и установке для них исправлений.

* Программное обеспечение, на котором не установлены исправления, по-прежнему подвергается большому количеству атак. Прокси приложения Azure AD — это глобальная служба корпорации Майкрософт, которая гарантирует применение новейших исправлений и обновлений безопасности.
* Чтобы повысить уровень безопасности приложений, опубликованных в прокси приложения Azure AD, мы запрещаем программам-обходчикам индексировать и архивировать ваши приложения. Каждый раз, когда программа-обходчик пытается получить параметры автоматической обработки для опубликованного приложения, прокси приложения отправляет ей файл robots.txt, который содержит `User-agent: * Disallow: /`.

## <a name="under-the-hood"></a>Как все устроено

Прокси приложения Azure AD состоит из двух частей:

* Облачная служба, в которой устанавливают соединения клиенты или пользователи.
* Соединитель прокси приложения Azure AD. Этот локальный компонент прослушивает запросы от службы прокси приложения Azure AD и обрабатывает подключения к внутренним приложениям. 

Между соединителем и службой прокси приложения устанавливается поток данных. Это происходит в следующих случаях:

* При первоначальной настройке соединителя.
* Когда соединитель обращается к службе прокси приложения, запрашивая сведения о конфигурации, в том числе о том, в какую группу соединителей он входит.
* Пользователь получает доступ к опубликованному приложению.

>[!NOTE]
>Весь обмен данными осуществляется по протоколу SSL и всегда инициируется соединителем службы прокси приложения. В службе используются только исходящие соединения.

Почти для всех вызовов соединитель использует сертификат клиента, чтобы пройти проверку подлинности в службе прокси приложения. Единственным исключением является этап начальной настройки, когда устанавливается сертификат клиента.

### <a name="installing-the-connector"></a>Установка соединителя

При начальной настройке соединителя выполняются события из следующей последовательности.

1. В процессе установки соединителя происходит его регистрация в службе. Сейчас для этого пользователю предлагается ввести учетные данные администратора Azure AD. Полученный маркер затем предъявляется службе прокси приложения Azure AD.
2. Прокси приложения оценивает маркер, чтобы убедиться, что пользователь является членом роли администратора компании в том клиенте, для которого выдан маркер. Если пользователь не является членом роли администратора, процесс завершается.
3. Соединитель генерирует запрос сертификата клиента и вместе с маркером передает его в службу прокси приложения, которая в свою очередь проверяет маркер и подписывает запрос сертификата клиента.
4. Соединитель использует этот сертификат клиента для последующего обмена данными со службой прокси приложения.
5. Соединитель обращается к службе, чтобы получить данные о конфигурации системы, предоставляя сертификат клиента. Теперь все готово, чтобы принимать запросы.

### <a name="updating-the-configuration-settings"></a>Обновление параметров конфигурации

Каждый раз, когда служба прокси приложения обновляет параметры конфигурации, выполняются события из следующей последовательности.

1. Соединитель подключается к конечной точке конфигурации в службе прокси приложения, передавая свой сертификат клиента.
2. После проверки сертификата клиента служба прокси приложения возвращает в соединитель данные о конфигурации (например, в какую группу должен входить этот соединитель).
3. Если с даты начала действия текущего сертификата прошло больше 30 дней, соединитель генерирует новый запрос сертификата. Таким образом, сертификат клиента обновляется каждые 30 дней.

### <a name="accessing-published-applications"></a>Доступ к опубликованным приложениям

Когда пользователь запрашивает доступ к опубликованному приложению, выполняются события из следующей последовательности.

#### <a name="the-application-proxy-service-checks-the-configuration-settings-for-the-app"></a>Служба прокси приложения проверяет параметры конфигурации приложения. 

Если в приложении используется предварительная проверка подлинности с помощью Azure AD, пользователи перенаправляются для проверки подлинности в службу маркеров безопасности Azure AD. Если приложение настроено для использования сквозного режима, этот шаг пропускается.

1. Прокси приложения проверяет соответствие всем требованиям политики условного доступа для конкретного приложения. Этот шаг позволяет гарантировать, что пользователь назначен приложению. Если требуется двухфакторная проверка подлинности, появляется запрос на второй метод проверки подлинности.

2. Когда все проверки выполнены, служба токенов безопасности Azure AD выдает для приложения подписанный токен и перенаправляет пользователя обратно в службу прокси приложения.

3. Прокси приложения проверяет, что токен выдан правильному приложению. Служба проверяет и другие данные, например подпись Azure AD и срок действия маркера.

4. Прокси приложения создает зашифрованный файл cookie для проверки подлинности, который будет подтверждать, что проверка подлинности в приложении успешно выполнена. Этот файл cookie содержит метку времени, которая обозначает окончание срока его действия, вычисляемое по данным маркера Azure AD и другим данным, таким как имя пользователя, по которому выполнялась проверка подлинности. Файл cookie шифруется с помощью закрытого ключа, известного только службе прокси приложения.

5. Прокси приложения перенаправляет пользователя к исходному запрошенному URL-адресу.

Если на любом из этапов предварительной проверки подлинности происходит сбой, запрос пользователя отклоняется с сообщением о причинах проблемы.


#### <a name="application-proxy-places-a-request-in-the-appropriate-queue-for-an-on-premises-connector-to-handle"></a>Прокси приложения помещает запрос в соответствующую очередь для обработки с помощью локального соединителя. 

Все запросы от соединителя к службе прокси приложения являются исходящими. Соединители не закрывают исходящее подключение для прокси приложения. Когда поступает новый запрос, прокси приложения добавляет его в очередь в одном из открытых подключений, чтобы его получил соединитель.

Запрос содержит данные для приложения, например заголовки запроса и данные из зашифрованного файла cookie, имя пользователя, отправившего запрос, и идентификатор запроса. Однако зашифрованный файл cookie, используемый для проверки подлинности, не передается в соединитель.

#### <a name="the-connector-receives-the-request-from-the-queue"></a>Соединитель получает запрос из очереди. 

На основе запроса прокси приложения выполняет одно из следующих действий.

* Соединитель подтверждает возможность идентификации приложения. Если приложение определить не удается, соединитель устанавливает подключение к службе прокси приложения для сбора сведений о приложении, а ответ кэширует локально.

* Если запрос является простой операцией (например, когда в тексте нет данных, как в случае с операцией *GET* RESTful), соединитель подключается к целевому внутреннему ресурсу и ожидает ответа.

* Если в тексте запроса содержатся данные, как в случае с операцией *POST* RESTful, соединитель устанавливает исходящее подключение к экземпляру прокси приложения, используя сертификат клиента. Это подключение используется для запроса данных и открытия подключения к внутреннему ресурсу. Получив запрос от соединителя, служба прокси приложения начинает принимать содержимое от пользователя и перенаправляет данные в соединитель. Соединитель, в свою очередь, перенаправляет данные во внутренний ресурс.

#### <a name="after-the-request-and-transmission-of-all-content-to-the-back-end-is-complete-the-connector-waits-for-a-response"></a>Когда завершается выполнение запроса и передача содержимого в серверную службу, соединитель ожидает ответа.

Получив этот ответ, соединитель устанавливает исходящее подключение к службе прокси приложения, чтобы передать сведения о заголовке и начать потоковую передачу возвращаемых данных.

#### <a name="application-proxy-streams-the-data-to-the-user"></a>Прокси приложения выполняет потоковую передачу данных пользователю. 

В зависимости от требований и параметров приложения может выполняться обработка заголовков.


## <a name="next-steps"></a>Дальнейшие действия

[Аспекты топологии сети при использовании прокси приложения Azure AD](application-proxy-network-topology-considerations.md)

[Сведения о соединителях прокси приложения Azure AD](application-proxy-understand-connectors.md)

