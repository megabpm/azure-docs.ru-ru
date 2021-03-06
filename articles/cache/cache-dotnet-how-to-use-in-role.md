---
title: "Как использовать кэш роли (.NET) | Документация Майкрософт"
description: "Сведения об использовании кэша роли Azure. Примеры написаны на языке C# и используют интерфейс .NET API."
services: cache
documentationcenter: .net
author: steved0x
manager: douge
editor: 
ms.assetid: 4dad61ec-422a-4618-8054-84ae4ce652a2
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: sdanie
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: e8347c21610118af4ebaa80b24edd0838bc58cf8
ms.lasthandoff: 03/18/2017

---
# <a name="how-to-use-in-role-cache-for-azure-cache"></a>Как использовать кэш роли для кэша Azure
> [!IMPORTANT]
> Согласно прошлогоднему [объявлению](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), использование управляемой службы кэша Azure и кэша роли Azure **было прекращено** 30 ноября 2016 года. Мы рекомендуем использовать [кэш Redis для Azure](https://azure.microsoft.com/services/cache/). Дополнительные сведения о переходе на новую службу см. в разделе [Перенос из управляемой службы кэша в кэш Redis для Azure](../redis-cache/cache-migrate-to-redis.md).
> 
> 

В этом руководстве объясняется, как приступить к работе с **кэшем роли для кэша Azure**. Примеры написаны на языке C\# и используют интерфейс API .NET. Рассматриваются разнообразные сценарии, в том числе **настройка кластера кэша**, **настройка клиентов кэша**, **добавление и удаление объектов в кэше, хранение состояния сеанса ASP.NET в кэше** и **включение кэширования вывода страниц ASP.NET с помощью кэша**. Дополнительную информацию об использовании кэша роли см. в разделе [Дальнейшие действия][Next Steps].



<a name="what-is"></a>

## <a name="what-is-in-role-cache"></a>Что такое кэш роли?
Кэш роли обеспечивает уровень кэширования в приложениях Azure. Кэширование повышает производительность путем временного хранения информации в памяти из других источников и может снизить расходы, связанные с транзакциями баз данных в облаке. Кэш роли включает следующие компоненты:

* Готовые поставщики ASP.NET для кэширования состояний сеанса и выводимых данных страниц, ускоряющие работу веб-приложений без изменения их кода.
* Кэширование любого сериализуемого управляемого объекта, например объектов среды CLR, строк, XML, двоичных данных.
* Согласованная модель разработки для Azure и Windows Server AppFabric.

Кэш роли предоставляет новый способ кэширования с использованием части памяти виртуальных машин, которые размещают экземпляры роли в ваших облачных службах Azure (также известных как размещенные службы). Пользователи получили большую гибкость с точки зрения параметров развертывания. Кэш может иметь очень большие размеры, квоты для конкретного кэша отсутствуют.

> [!IMPORTANT]
> Начиная с пакета SDK для Azure версии 2.6, кэш роли использует пакет SDK для службы хранилища Microsoft Azure версии 4.3. В предыдущих версиях пакета SDK для Azure для кэша роли использовался пакет SDK для службы хранилища Azure версии 1.7. Приложения, использующие кэш роли с пакетами SDK для Azure до версии 2.6, следует перенести в пакет SDK для Azure версии 2.6 до того, как служба хранилища Azure версии 2011-08-18 будет выведена из эксплуатации (1 августа 2016 года). Дополнительные сведения см. в статье [Заметки о выпуске пакета SDK 2.6 для Azure. Кэш роли](../app-service-web/azure-sdk-dotnet-release-notes-2-6.md#in-role-cache-updates) и [Обновление по прекращению использования версий службы хранилища Microsoft Azure: продление срока до 2016 года](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).
> 
> 

Кэширование в экземплярах роли имеет следующие преимущества:

* Отсутствие дополнительных затрат на кэширование. Вы оплачиваете только вычислительные ресурсы, на которых размещается кэш.
* Исключение квот и регулирования для кэша.
* Расширенный контроль и улучшенная изоляция. 
* Повышение производительности.
* Автоматическое определение размера кэша при масштабировании ролей. эффективное масштабирование памяти, доступной для кэширования, с увеличением или уменьшением объема при добавлении и удалении экземпляров ролей;
* Полнофункциональная отладка во время разработки. 
* Поддержка протокола memcache.

Кроме того, кэширование в экземплярах роли предлагает следующие настраиваемые параметры:

* Настройка выделенной роли для кэширования или совмещение кэша для существующих ролей. 
* Доступ к кэшу для нескольких клиентов в одном развертывании облачной службы.
* Создание нескольких именованных кэшей с различными свойствами.
* Дополнительная настройка высокого уровня доступности для отдельных кэшей.
* Использование расширенных возможностей кэширования, таких как регионы, теги и уведомления.

Это руководство содержит обзор, посвященный началу работы с кэшем роли. Дополнительные сведения о возможностях, выходящих за рамки данного руководства по началу работы, см. в разделе [Обзор кэша роли][Overview of In-Role Cache].

<a name="getting-started-cache-role-instance"></a>

## <a name="getting-started-with-in-role-cache"></a>Приступая к работе с кэшем роли
Кэш роли позволяет включить кэширование с использованием памяти, которая находится на виртуальных машинах, в которых размещаются ваши экземпляры роли. Экземпляры роли, в которых размещается кэш, называются **кластер кэша**. Существует две топологии развертывания для кэширования в экземплярах роли.

* **Выделенные роли** для кэширования — такие экземпляры роли используются исключительно для кэширования.
* **Совмещенная роль** для кэширования — кэш использует ресурсы виртуальной машины (полоса пропускания, ЦП и память) совместно с приложением.

Чтобы использовать кэширование в экземплярах роли, необходимо настроить кластер кэша, а затем настроить клиенты кэша таким образом, чтобы они имели доступ к кластеру кэша.

* [Настройка кластера кэша][Configure the cache cluster]
* [Настройка клиентов кэша][Configure the cache clients]

<a name="enable-caching"></a>

## <a name="configure-the-cache-cluster"></a>Настройка кластера кэша
Чтобы настроить кластер кэша **Совмещенная роль** , выберите роль, в которой вы хотите разместить кластер кэша. Щелкните свойства роли правой кнопкой мыши в **обозревателе решений** и выберите пункт **Свойства**.

![RoleCache1][RoleCache1]

Перейдите на вкладку **Кэширование**, установите флажок **Включить кэширование**, а затем задайте нужные параметры кэширования. Если включено кэширование в роли **Рабочая роль** или **Веб-роль ASP.NET**, конфигурация по умолчанию — **Совмещенная роль** для кэширования, при этом для кэширования выделено 30 % памяти экземпляров роли. Кэш по умолчанию настраивается автоматически, при необходимости можно создать дополнительный именованный кэш, который будет совместно использовать выделенную память.

![RoleCache2][RoleCache2]

Чтобы настроить кластер кэша **Выделенная роль**, добавьте в проект **Кэш рабочей роли**.

![RoleCache7][RoleCache7]

При добавлении в проект узла **Кэш рабочей роли** по умолчанию используется конфигурация **Выделенная роль** для кэширования.

![RoleCache8][RoleCache8]

После включения кэширования можно настроить учетную запись хранения для кластера кэша. Для кэша роли требуется учетная запись хранения Azure. Эта учетная запись хранения используется для хранения данных конфигурации для кластера кэша, к которым обращаются все виртуальные машины, входящие в состав кластера кэша. Эта учетная запись хранения указывается на вкладке **Кэширование** страницы свойств роли кластера кэша сразу над разделом **Параметры именованного кэша**.

![RoleCache10][RoleCache10]

> Если эта учетная запись хранения не настроена, роли не смогут запуститься. 
> 
> 

Размер кэша определяется сочетанием размера ВМ роли, числом экземпляров роли, а также настройкой кластера кэша как выделенной роли или совмещенной роли.

> Этот раздел содержит упрощенный обзор настройки размера кэша. Дополнительные сведения о размере кэша и других вопросах планирования емкости см. в разделе [Планирование емкости для управляемой службы кэша Azure][In-Role Cache Capacity Planning Considerations].
> 
> 

Чтобы настроить размер виртуальной машины и число экземпляров роли, щелкните свойства роли правой кнопкой мыши в **обозревателе решений** и выберите пункт **Свойства**.

![RoleCache1][RoleCache1]

Переключитесь на вкладку **Конфигурация** . Значение по умолчанию параметра **Число экземпляров** — 1, а параметра **Размер виртуальной машины** — **Small** (Малый).

![RoleCache3][RoleCache3]

Общий объем памяти для размеров виртуальной машины выглядит следующим образом: 

* **Малый**: 1,75 ГБ;
* **Средний**: 3,5 ГБ;
* **Большой**: 7 ГБ;
* **Сверхбольшой**: 14 ГБ.

> Эти размеры памяти отражают общий объем памяти, доступный для виртуальной машины, который совместно используется для ОС, процесса кэша, кэширования данных и приложения. Дополнительные сведения о настройке размеров виртуальных машин см. в разделе [Размеры для облачных служб][How to Configure Virtual Machine Sizes]. Обратите внимание, что кэш не поддерживается в виртуальных машинах размера **Сверхмалый**.
> 
> 

Если указано кэширование **Совмещенная роль** , размер кэша определяется как указанный процент от памяти виртуальной машины. Если указано кэширование **Выделенная роль** , для кэширования используется вся доступная память виртуальной машины. Если заданы два экземпляра роли, используется общая память виртуальных машин. Это формирует кластер кэша, в котором доступная память кэширования распределена между несколькими экземплярами роли, но для клиентов кэша представлена как единый ресурс. Настройка дополнительных экземпляров роли увеличивает размер кэша таким же образом. Чтобы определить параметры, необходимые для подготовки кэша требуемого размера, можно использовать таблицу по планированию емкости, которая описывается в разделе [Планирование емкости для управляемой службы кэша Azure][In-Role Cache Capacity Planning Considerations].

После настройки кластера кэша можно настроить клиентов кэша таким образом, чтобы обеспечить им доступ к кэшу.

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Настройка клиентов кэша
Для доступа к кэшу роли клиенты должны находиться в одном развертывании. Если кластер кэша является кластером кэша выделенной роли, клиенты являются другими ролями в развертывании. Если кластер кэша является кластером кэша совмещенной роли, клиенты должны быть другими ролями в развертывании или самими ролями, в которых размещается кластер роли. Доступен пакет NuGet, который может быть использован для настройки каждой из ролей клиента, обращающихся к кэшу. Чтобы настроить роль для доступа к кластеру кэша с использованием пакета NuGet для кэширования, щелкните правой кнопкой мыши проект роли в **обозревателе решений** и выберите команду **Manage NuGet Packages** (Управление пакетами NuGet). 

![RoleCache4][RoleCache4]

Выберите **In-Role Cache** (Кэш роли), щелкните **Установить**, а затем — **Принимаю**.

> Если элемент **In-Role Cache** (Кэш роли) не отображается в списке, введите **WindowsAzure.Caching** в текстовом поле **Поиск в Интернете** и выберите его в результатах.
> 
> 

![RoleCache5][RoleCache5]

Пакет NuGet выполняет несколько задач: добавляет требуемую конфигурацию в файл конфигурации роли кэша, добавляет параметр уровня диагностики клиента в файл ServiceConfiguration.cscfg приложения Azure и добавляет необходимые ссылки на сборки.

> Для веб-ролей ASP.NET пакет NuGet для Caching также добавляет два закомментированных раздела в файл web.config. Первый раздел позволяет сохранить состояние сеанса в кэше, а второй раздел включает кэширование вывода страниц ASP.NET. Дополнительную информацию см. в разделах [Практическое руководство. Хранение состояния сеанса ASP.NET в кэше] и [Практическое руководство. Хранение кэширования выводимых данных страниц ASP.NET в кэше][How To: Store ASP.NET Page Output Caching in the Cache].
> 
> 

Пакет NuGet добавляет перечисленные ниже элементы конфигурации в файл web.config или app.config вашей роли. Разделы **dataCacheClients** и **cacheDiagnostics** добавляются в элемент **configSections**. При отсутствии элемента **configSections** он создается как дочерний объект элемента **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />

      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

Эти новые разделы содержат ссылки на элементы **dataCacheClients** и **cacheDiagnostics**. Эти элементы также добавляются к элементу **configuration** .

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

После добавления конфигурации замените **[имя роли кластера кэша]** именем роли, в которой размещен кластер кэша.

> Если **[имя роли кластера кэша]** не заменяется именем роли, в которой размещен кластер кэша, то при обращении к кэшу порождается исключение **TargetInvocationException** с внутренним исключением **DatacacheException** с сообщением No such role exists (Такой роли не существует).
> 
> 

Пакет NuGet также добавляет параметр **ClientDiagnosticLevel** в **ConfigurationSettings** роли клиента кэша в файле ServiceConfiguration.cscfg. Ниже приведен пример раздела **WebRole1** из файла ServiceConfiguration.cscfg с **ClientDiagnosticLevel**, равным 1, что соответствует значению **ClientDiagnosticLevel** по умолчанию.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Кэш роли предоставляет уровень диагностики как сервера, так и клиента кэша. Уровень диагностики — отдельный параметр, который определяет уровень диагностических сведений, собираемых для кэширования. Дополнительные сведения см. в разделе [Диагностика и устранение неполадок кэша роли Azure][Troubleshooting and Diagnostics for In-Role Cache].
> 
> 

Пакет NuGet также добавляет ссылки на следующие сборки:

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Если проект является веб-ролью ASP.NET, также добавляется следующая ссылка сборки:

* Microsoft.Web.DistributedCache.dll.

После настройки проекта клиента для кэширования можно использовать методы, описанные в следующих разделах, для работы с кэшем.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Работа с кэшами
Действия, приведенные в этом разделе, описывают способы выполнения типовых задач при работе с кэшированием.

* [Практическое руководство. Создание объекта DataCache][How To: Create a DataCache Object]
* [Практическое руководство. Добавление и извлечение объекта из кэша][How To: Add and Retrieve an Object from the Cache]
* [Практическое руководство. Указание срока действия объекта в кэше][How To: Specify the Expiration of an Object in the Cache]
* [Практическое руководство. Хранение состояния сеанса ASP.NET в кэше][Практическое руководство. Хранение состояния сеанса ASP.NET в кэше]
* [Практическое руководство. Хранение кэширования выводимых данных страниц ASP.NET в кэше][How To: Store ASP.NET Page Output Caching in the Cache]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>Практическое руководство. Создание объекта DataCache
Чтобы программно работать с кэшем, требуется ссылка на кэш. Добавьте следующий код в верхнюю часть любого файла, из которого вы хотите использовать кэш роли:

    using Microsoft.ApplicationServer.Caching;

> Если Visual Studio не распознает типы в операторе using даже после установки пакета NuGet для кэширования, который добавляет необходимые ссылки, убедитесь, что целевым профилем для проекта является платформа .NET Framework 4.0 или более поздней версии, и не забудьте выбрать один из профилей, в котором не указан **клиентский профиль**. Инструкции по настройке клиентов кэша см. в разделе [Настройка клиентов кэша][Configure the cache clients].
> 
> 

Существует два способа создать объект **DataCache** . Первый способ — просто создайте **DataCache**, передав имя нужного кэша.

    DataCache cache = new DataCache("default");

После создания экземпляра **DataCache** его можно использовать для взаимодействия с кэшем, как описано в следующих разделах.

Чтобы использовать второй способ, создайте в своем приложении новый объект **DataCacheFactory** с помощью конструктора по умолчанию. В этом случае клиент кэша будет использовать параметры в файле конфигурации. Вызовите метод **GetDefaultCache** нового экземпляра **DataCacheFactory**, который возвращает объект **DataCache**, или вызовите метод **GetCache** и передайте имя своего кэша. Эти методы возвращают объект **DataCache** , который затем можно использовать для программного доступа к кэшу.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>Практическое руководство. Добавление и извлечение объекта из кэша
Чтобы добавить элемент в кэш, можно использовать метод **Add** или **Put**. Метод **Add** добавляет указанный объект в кэш, ключом которого является значение параметра ключа.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Если объект с тем же ключом уже находится в кэше, порождается исключение **DataCacheException** со следующим сообщением:

> ErrorCode:SubStatus. Предпринята попытка создать объект с ключом, который уже существует в кэше. Кэш принимает только уникальные значения ключа для объектов.
> 
> 

Чтобы извлечь объект с указанным ключом, можно воспользоваться методом **Get** . Если объект существует, он возвращается, а если нет, возвращается значение null.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

Метод **Put** добавляет объект с указанным ключом в кэш, если он не существует, или заменяет существующий объект.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>Практическое руководство. Указание срока действия объекта в кэше
По умолчанию срок действия элементов в кэше истекает через 10 минут после их помещения в кэш. Это значение может быть настроено в параметре **Время жизни (мин)** в свойствах роли, где размещен кластер кэша.

![RoleCache6][RoleCache6]

Существует три типа значений для параметра **Тип окончания срока действия**: **None** (Никогда), **Абсолютный** и **Скользящее окно**. Эти значения определяют применение параметра **Время жизни (мин)** для задания срока действия. По умолчанию для параметра **Тип срока действия** задано значение **Абсолютный**. Это означает, что таймер обратного отсчета срока действия элемента запускается в момент помещения элемента в кэш. После истечения указанного периода времени срок действия элемента заканчивается. Если указано значение **Скользящее окно** , отсчет срока действия элемента сбрасывается при каждом обращении к элементу в кэше и срок действия элемента истекает только по прошествии указанного периода с момента последнего обращения к элементу. Если указано значение**None** (Никогда), то для параметра **Срок жизни (мин)** следует задать значение **0**, в результате чего элементы будут оставаться действительными все время, пока они находятся в кэше.

Если требуется использовать более короткий или длинный интервал ожидания по сравнению с настроенным в свойствах роли, можно задать конкретную длительность при добавлении или обновлении элемента в кэше с помощью перегрузки методов **Add** и **Put**, принимающей параметр **TimeSpan**. В следующем примере строка **value** добавляется в кэш с ключом **item** и временем ожидания 30 минут.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Чтобы просмотреть оставшееся время ожидания для элемента в кэше, можно использовать метод **GetCacheItem** для получения объекта **DataCacheItem**, содержащего данные об элементе в кэше, в том числе об оставшемся времени ожидания.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>Практическое руководство. Хранение состояния сеанса ASP.NET в кэше
Поставщик состояний сеансов для кэша роли представляет собой механизм внепроцессного хранения для приложений ASP.NET. Этот поставщик позволяет хранить состояние сеанса в кэше Azure, а не в памяти или в базе данных SQL Server. Чтобы использовать поставщик состояний сеансов кэширования, сначала настройте кластер кэша, а затем настройте приложение ASP.NET для кэширования с помощью пакета NuGet для кэша, как описано в разделе [Приступая к работе с кэшем роли][Getting Started with In-Role Cache]. При установке пакета кэша NuGet для Caching он добавляет в файл web.config закомментированный раздел с требуемой конфигурацией, позволяющей приложению ASP.NET использовать поставщик состояний сеансов для кэша роли.

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Если ваш файл web.config не содержит этот закомментированный раздел после установки пакета NuGet для кэша, убедитесь, что установлена самая последняя версия диспетчера пакетов NuGet [отсюда][NuGet Package Manager Installation], после чего удалите и переустановите этот пакет.
> 
> 

Чтобы включить поставщик состояний сеансов для кэша роли, раскомментируйте указанный раздел. Кэш по умолчанию указан в представленном фрагменте кода. Чтобы использовать другой кэш, укажите его в атрибуте **cacheName** .

Дополнительную информацию об использовании поставщика состояний сеансов для службы кэширования см. в разделе [Поставщик состояния сеансов для кэша роли Azure][Session State Provider for In-Role Cache].

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>Практическое руководство. Хранение кэширования выводимых данных страниц ASP.NET в кэше
Поставщик кэша вывода для кэша роли представляет собой механизм внепроцессного хранения для выходных данных кэширования. Эти данные предназначены специально для полных HTTP-ответов (кэширование вывода страниц). Поставщик подключается к новой точке расширения поставщика вывода кэша, которая появилась в ASP.NET 4. Чтобы воспользоваться поставщиком выходного кэша, сначала настройте свой кластер кэша, а затем настройте приложение ASP.NET для кэширования с помощью пакета NuGet для кэша, как описано в разделе [Приступая к работе с кэшем роли][Getting Started with In-Role Cache]. При установке пакета кэша NuGet для Caching он добавляет в файл web.config следующий закомментированный раздел с требуемой конфигурацией, позволяющей приложению ASP.NET использовать поставщик кэша вывода для кэша роли.

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Если ваш файл web.config не содержит этот закомментированный раздел после установки пакета NuGet для кэша, убедитесь, что установлена самая последняя версия диспетчера пакетов NuGet [отсюда][NuGet Package Manager Installation], после чего удалите и переустановите этот пакет.
> 
> 

Чтобы включить поставщик кэша вывода для кэша роли, раскомментируйте указанный раздел. Кэш по умолчанию указан в представленном фрагменте кода. Чтобы использовать другой кэш, укажите его в атрибуте **cacheName** .

Добавьте директиву **OutputCache** для каждой страницы, для которой требуется кэшировать выходные данные.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

В этом примере кэшированные данные страницы будут оставаться в кэше в течение 60 секунд, а для каждой комбинации параметров будет кэшироваться другая версия страницы. Дополнительные сведения о доступных параметрах см. в разделе [Директива OutputCache][OutputCache Directive].

Дополнительные сведения об использовании поставщика кэша вывода для кэша роли см. в разделе [Поставщик кэша вывода для кэша роли Azure][Output Cache Provider for In-Role Cache].

<a name="next-steps"></a>

## <a name="next-steps"></a>Дальнейшие действия
После изучения основ кэша роли просмотрите следующие ссылки, чтобы ознакомиться с тем, как выполнять более сложные задачи по кэшированию.

* Ознакомьтесь со справочником MSDN: [Кэш роли][In-Role Cache].
* Узнайте о том, как перенести данные в кэш роли: [Переход на кэш роли Azure][Migrate to In-Role Cache].
* Ознакомьтесь с примерами: [Примеры кэша роли Azure][In-Role Cache Samples].
* Посмотрите видео [Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching][Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching] (Максимальная производительность. Ускорение работы приложений облачных служб с помощью кэширования Azure) про кэш роли от TechEd 2013.

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with In-Role Cache]: #getting-started-cache-role-instance
[Configure the cache cluster]: #enable-caching
[Configure the desired cache size]: #cache-size
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[Практическое руководство. Хранение состояния сеанса ASP.NET в кэше]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net

<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png

<!-- LINKS -->
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[In-Role Cache Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=252651
[In-Role Cache Samples]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[In-Role Cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Migrate to In-Role Cache]: http://msdn.microsoft.com/library/hh914163.aspx
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Output Cache Provider for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Overview of In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=254172
[Session State Provider for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Troubleshooting and Diagnostics for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me


