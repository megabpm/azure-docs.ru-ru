# Обзор
## [Что такое Azure AD Connect?](active-directory-aadconnect.md)
## [Что такое синхронизация Azure AD Connect?](active-directory-aadconnectsync-whatis.md)
### [Пользователи и контакты](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Архитектура](active-directory-aadconnectsync-understanding-architecture.md)
### [Декларативная подготовка](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Выражения декларативной подготовки](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Конфигурация по умолчанию](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Что такое Azure AD Connect и федерация?](active-directory-aadconnectfed-whatis.md)


# Приступая к работе
## [Предварительные требования](active-directory-aadconnect-prerequisites.md)
## [Установка Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Стандартные параметры](active-directory-aadconnect-get-started-express.md)
### [Настраиваемые параметры](active-directory-aadconnect-get-started-custom.md)
### [Обновление из DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Установка новой версии](active-directory-aadconnect-upgrade-previous-version.md)


# Практическое руководство
## планирование и разработка
### [Принципы проектирования](active-directory-aadconnect-design-concepts.md)
### [Топологии Azure AD Connect.](active-directory-aadconnect-topologies.md)
### [Единый вход](active-directory-aadconnect-sso.md)
### [Службы федерации Active Directory в Azure](active-directory-aadconnect-azure-adfs.md)
### [Специальные рекомендации для экземпляров](active-directory-aadconnect-instances.md)
### [Если у вас уже есть клиент Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Управление Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Обновление сертификатов для Office 365 и Azure AD](active-directory-aadconnect-o365-certs.md)
### [Включение обратной записи устройства](active-directory-aadconnect-feature-device-writeback.md)
### [Параметры единого входа пользователей](active-directory-aadconnect-user-signin.md)
### [Поддержка нескольких доменов для федерации](active-directory-aadconnect-multiple-domains.md)
### [Автоматическое обновление](active-directory-aadconnect-feature-automatic-upgrade.md)
### [Использование поставщика удостоверений (IdP) SAML 2.0 для единого входа](active-directory-aadconnect-federation-saml-idp.md)



## Управление синхронизацией Azure AD Connect
### [Предотвращение случайного удаления](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Синхронизация паролей](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Сквозная проверка подлинности](active-directory-aadconnect-pass-through-authentication.md)
### [Учетная запись службы Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Мастер установки](active-directory-aadconnectsync-installation-wizard.md)
### [Изменение конфигурации по умолчанию](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Настройка фильтрации](active-directory-aadconnectsync-configure-filtering.md)
### [Планировщик](active-directory-aadconnectsync-feature-scheduler.md)
### [Расширения каталогов](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Изменение пароля учетной записи службы Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Изменение пароля учетной записи AD DS](active-directory-aadconnectsync-change-addsacct-pass.md)
### [Включение корзины AD](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Операции](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Соединители](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Конструктор метавселенной](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Поиск в метавселенной](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Управление службами федерации
### [Управление и настройка](active-directory-aadconnect-federation-management.md)
### [Федерация нескольких экземпляров Azure AD с одним экземпляром AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Устранение неполадок
### [Соединение](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Ошибки при синхронизации](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [Объект не синхронизирован](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Синхронизация паролей](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [Ошибка LargeObject, вызванная userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [Восстановление из LocalDB с ограничением в 10 ГБ](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Справочные материалы
## [Синхронизация удостоверений и устойчивость повторяющихся атрибутов](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Порты и протоколы, необходимые для гибридной идентификации](active-directory-aadconnect-ports.md)
## [Возможности предварительной версии](active-directory-aadconnect-feature-preview.md)
## [Журнал версий](active-directory-aadconnect-version-history.md)
## [Учетные записи и разрешения](active-directory-aadconnect-accounts-permissions.md)

## Синхронизация Azure AD Connect
### [Атрибуты, синхронизируемые с Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Журнал выпуска версий соединителей](active-directory-aadconnectsync-connector-version-history.md)
### [Справочник по функциям](active-directory-aadconnectsync-functions-reference.md)
### [Рабочие задачи и рекомендации](active-directory-aadconnectsync-operations.md)
### [Список совместимости с федерацией Azure AD](active-directory-aadconnect-federation-compatibility.md)
### [Технические понятия](active-directory-aadconnectsync-technical-concepts.md)
### [Функции службы](active-directory-aadconnectsyncservice-features.md)




# Сопутствующие материалы
## [Мониторинг локальной инфраструктуры идентификации и служб синхронизации в облаке.](../connect-health/active-directory-aadconnect-health.md)
## [Руководство по проектированию гибридной идентификации](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Ресурсы
##[Azure AD Connect: вопросы и ответы](active-directory-aadconnect-faq.md)
##[Нерекомендуемая версия DirSync](active-directory-aadconnect-dirsync-deprecated.md)
