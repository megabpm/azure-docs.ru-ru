---
title: "Руководство по ASP.NET MVC для Azure Cosmos DB. Разработка веб-приложения | Документация Майкрософт"
description: "Это руководство по MVC для ASP.NET поможет вам создать веб-приложение MVC с использованием Azure Cosmos DB. С помощью этого руководства по ASP NET MVC вы сможете сохранить файл JSON и получить доступ к данным из приложения \"Список дел\", размещенного на веб-сайтах Azure."
keywords: "руководство по asp.net mvc, разработка веб-приложений, веб-приложение mvc, пошаговое руководство asp net mvc"
services: cosmos-db
documentationcenter: .net
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: syamk
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 6623265ea98370db74fd9e2061d1128c44e9cd0e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="_Toc395809351"></a>Руководство по ASP.NET MVC. Разработка веб-приложений в Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Чтобы показать, как эффективно использовать Azure Cosmos DB для хранения документов JSON и их запроса, в этой статье приведена пошаговая инструкция для создания приложения со списком задач с помощью Azure Cosmos DB. Задачи будут храниться в виде документов JSON в Azure Cosmos DB.

![Снимок экрана: веб-приложение "Список дел", созданное с помощью этого руководства. Пошаговое руководство по ASP.NET MVC](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

В этом пошаговом руководстве показан пример использования службы Azure Cosmos DB, предоставляемой Azure, для хранения данных и доступа к ним из веб-приложения MVC для ASP.NET, размещенного на платформе Azure. Если вы ищете руководство, в котором рассматривается только Azure Cosmos DB, а не компоненты ASP.NET MVC, дополнительные сведения см. в статье о [создании консольного приложения Azure Cosmos DB на языке C#](documentdb-get-started.md).

> [!TIP]
> Предполагается, что у вас есть опыт использования ASP.NET MVC и веб-сайтов Azure. Если вы никогда не работали с ASP.NET или [необходимыми инструментами](#_Toc395637760), мы рекомендуем скачать полный пример проекта с портала [GitHub][GitHub] и следовать инструкциям в этом примере. После выполнения сборки вы можете просмотреть эту статью, чтобы разобраться в коде этого проекта.
> 
> 

## <a name="_Toc395637760"></a>Предварительные требования для изучения этого учебника по базам данных
Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/). 

    ИЛИ

    Локальная установка [эмулятора Azure Cosmos DB](local-emulator.md).
* [Visual Studio 2015](http://www.visualstudio.com/) , Visual Studio 2013 с обновлением 4 или выше. Если используется Visual Studio 2013, необходимо установить [пакет NuGet Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers/) , чтобы добавить поддержку C# 6.0. 
* Пакет Azure SDK для .NET версии 2.5.1 или более поздней, доступный в [установщике веб-платформы Майкрософт][Microsoft Web Platform Installer].

Все снимки экранов в этой статье сделаны с помощью Visual Studio 2013 с обновлением 4 и пакетом Azure SDK для .NET версии 2.5.1. Если ваша система сконфигурирована с различными версиями программ, то вполне вероятно, что ваши экраны и опции не будет соответствовать полностью, но если Вы выполните все вышеуказанные требования, то это решение должно работать.

## <a name="_Toc395637761"></a>Шаг 1. Создание учетной записи базы данных Azure Cosmos DB
Давайте сначала создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись или вы используете эмулятор Azure Cosmos DB для этого руководства, вы можете перейти к разделу [создания приложения ASP.NET MVC](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Теперь рассмотрим создание нового приложения ASP.NET MVC с нуля. 

## <a name="_Toc395637762"></a>Шаг 2. Создание нового приложения ASP.NET MVC
Теперь, когда у вас есть учетная запись, создадим наш новый проект ASP.NET.

1. В меню Visual Studio **Файл** выберите **Создать**, а затем щелкните **Проект**.
   
       The **New Project** dialog box appears.
2. На панели **Типы проектов** разверните **Шаблоны**, **Visual C#**, **Веб**, а затем выберите **Веб-приложение ASP.NET**.
   
      ![Снимок экрана: диалоговое окно "Новый проект" с выделенным типа проекта веб-приложения ASP.NET](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)
3. В поле **Имя** введите имя проекта. В этом учебнике используется имя todo. Если вы решили использовать другое имя, в ситуациях, когда будет идти речь о пространстве имен todo, вы должны будете откорректировать представленные образцы кода, используя имя своего приложения. 
4. Щелкните **Обзор**, чтобы перейти к папке, в которой вы хотите создать проект, и нажмите кнопку **ОК**.
   
      Откроется диалоговое окно **Новый проект ASP.NET** .
   
      ![Снимок экрана: диалоговое окно "Новый проект ASP.NET" с выделенным в нем шаблоном MVC и выбранным параметром "Разместить в облаке"](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)
5. На панели «Шаблоны» выберите **MVC**.
6. Если вы планируете разместить свое приложение в Azure, установите флажок **Разместить в облаке** в правом нижнем углу. Мы выбрали размещение в облаке и запуск приложения, размещенного на веб-сайтах Azure. При выборе этой опции будет осуществлена предварительная подготовка веб-сайта Azure, что упростит развертывание готового рабочего приложения. Если вы не хотите заранее настраивать Azure или предпочитаете разместить приложение в другом месте, снимите флажок возле параметра **Разместить в облаке**.
7. Нажмите кнопку **OK** , и Visual Studio создаст пустой шаблон ASP.NET MVC. 

    При появлении ошибки An error occurred while processing your request ("Возникла ошибка в процессе обработки запроса") решение см. в разделе [Устранение неполадок](#troubleshooting).

8. Если вы выбрали размещение в облаке, вы увидите по крайней мере еще один экран, на котором будет предложено войти в учетную запись Azure и ввести некоторые значения для нового веб-сайта. Укажите все дополнительные значения и продолжите. 
   
      Мы не выбираем "Сервер базы данных", так как не будем использовать сервер базы данных SQL Azure. Мы собираемся позднее создать учетную запись Azure Cosmos DB на портале Azure.
   
    Дополнительные сведения о выборе **плана службы приложений** и **группы ресурсов** см. в [подробном обзоре планов службы приложений Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
      ![Снимок экрана: диалоговое окно "Настройка сайта Microsoft Azure"](./media/documentdb-dotnet-application/image11_1.png)
9. После того как Visual Studio закончит создание шаблонного приложения MVC, у вас будет пустое приложение ASP.NET, которое можно запустить локально.
   
    Мы не станем запускать этот проект локально, потому что я уверена, что мы все видели приложение ASP.NET "Hello World". Давайте перейдем к добавлению Azure Cosmos DB в этот проект и созданию приложения.

## <a name="_Toc395637767"></a>Шаг 3. Добавление Azure Cosmos DB в проект веб-приложения MVC
Теперь, когда мы установили большую часть коммуникаций MVC для ASP.NET, необходимых для этого решения, перейдем к основной цели этого руководства, т. е. к добавлению Azure Cosmos DB в веб-приложение MVC.

1. DocumentDB .NET SDK комплектуется и распространяется как пакет NuGet. Чтобы получить пакет NuGet в Visual Studio, используйте диспетчер пакетов NuGet в Visual Studio: щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.
   
      ![Снимок экрана параметров контекстного меню проекта веб-приложения в обозревателе решений с выделенным параметром "Управление пакетами NuGet".](./media/documentdb-dotnet-application/image21.png)
   
    Откроется диалоговое окно **Управление пакетами NuGet** .
2. В поле **обзора** NuGet введите ***Azure Cosmos DB***.
   
    В результатах поиска найдите пакет **клиентской библиотеки Microsoft Azure Cosmos DB** и установите его. При этом будет загружен и установлен пакет Azure Cosmos DB, а также все зависимости, такие как Newtonsoft.Json. В окне **предварительного просмотра** щелкните **ОК**, после чего в окне **Принятие условий лицензионного соглашения** выберите **Принять**. Установка будет завершена.
   
      ![Снимок экрана: окно "Управление пакетами NuGet" с выделенной библиотекой клиента Microsoft Azure DocumentDB](./media/documentdb-dotnet-application/nuget.png)
   
      Для установки пакета можно также воспользоваться консолью диспетчера пакетов. Для этого в меню **Инструменты** щелкните **Диспетчер пакетов NuGet**, а затем выберите **Консоль диспетчера пакетов**. Когда появится запрос, введите следующую команду.
   
        Install-Package Microsoft.Azure.DocumentDB
3. После установки пакета в решение Visual Studio будут добавлены две ссылки, Microsoft.Azure.Documents.Client и Newtonsoft.Json, и оно будет выглядеть следующим образом.
   
      ![Снимок экрана: две ссылки, добавленные в проект данных JSON в обозревателе решений](./media/documentdb-dotnet-application/image22.png)

## <a name="_Toc395637763"></a>Шаг 4. Настройка приложения ASP.NET MVC
Теперь добавим модели, представления и контроллеры в это приложение MVC.

* [Добавление модели](#_Toc395637764).
* [Добавление контроллера](#_Toc395637765).
* [Добавление представлений](#_Toc395637766).

### <a name="_Toc395637764"></a>Добавление модели данных JSON
Давайте начнем с буквы **M** из аббревиатуры MVC, с модели (Model). 

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Модели**, выберите **Добавить**, а затем щелкните **Класс**.
   
      Откроется диалоговое окно **Добавление нового элемента** .
2. Назовите новый класс **Item.cs** и нажмите кнопку **Добавить**. 
3. В этом новом файле **Item.cs** добавьте следующий код после последнего *оператора using*.
   
        using Newtonsoft.Json;
4. Теперь замените этот код 
   
        public class Item
        {
        }
   
    следующим кодом:
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Все данные в Azure Cosmos DB передаются по сети и сохраняются в виде JSON-файлов. Для управления сериализацией и десериализацией объектов с помощью JSON.NET можно использовать атрибут **JsonProperty**, как показано на примере только что созданного класса **Item**. Вам **не обязательно** это делать, но, присваивая имена свойствам, я придерживаюсь правил наименования camelCase JSON. 
   
    Можно не только контролировать формат имени свойства при его поступлении в JSON, но и полностью переименовывать свойства .NET, как мы сделали со свойством **Description**. 

### <a name="_Toc395637765"></a>Добавление контроллера
С буквой **M** (моделями) разобрались, теперь займемся **C**, т. е. классом контроллера.

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Контроллеры**, выберите **Добавить**, а затем щелкните **Контроллер**.
   
    Откроется диалоговое окно **Добавление элемента формирования шаблонов** .
2. Выберите **Контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**.
   
    ![Снимок экрана: диалоговое окно "Добавление элемента формирования шаблонов" с выделенным параметром "Контроллер MVC 5 — Пустой"](./media/documentdb-dotnet-application/image14.png)
3. Назовите новый контроллер **ItemController.**
   
    ![Снимок экрана: диалоговое окно "Добавление контроллера"](./media/documentdb-dotnet-application/image15.png)
   
    После создания файла решение Visual Studio с новым файлом ItemController.cs в **Обозревателе решений**должно выглядеть следующим образом. Новый файл Item.cs, созданный ранее, также отображается.
   
    ![Снимок экрана решения Visual Studio: обозреватель решений с выделенным новым файлом ItemController.cs и файлом Item.cs](./media/documentdb-dotnet-application/image16.png)
   
    Можно закрыть файл ItemController.cs, мы вернемся к нему позже. 

### <a name="_Toc395637766"></a>Добавление представлений
И наконец, займемся буквой **V** из аббревиатуры MVC, т. е. представлениями.

* [Добавление представления «Индекс элементов»](#AddItemIndexView).
* [Добавление представления «Создание элементов»](#AddNewIndexView).
* [Добавление представления «Редактирование элементов»](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Добавление представления «Индекс элементов»
1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши пустую папку **Элемент**, созданную Visual Studio при добавлении **ItemController** ранее, выберите **Добавить**, а затем щелкните **Представление**.
   
    ![Снимок экрана: обозреватель решений, отображающий папку "Элемент", созданную Visual Studio, с выделенными командами "Добавления представления"](./media/documentdb-dotnet-application/image17.png)
2. В диалоговом окне **Добавление представления** выполните следующие действия.
   
   * В поле **Имя представления** введите ***Индекс***.
   * В поле **Шаблон** выберите ***Список***.
   * В поле **Класс модели** выберите ***Item (todo.Models)*** (Элемент (todo.Models)).
   * Оставьте пустым поле **Класс контекста данных** . 
   * В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
     
     ![Снимок экрана: диалоговое окно "Добавление представления"](./media/documentdb-dotnet-application/image18.png)
3. После установки этих значений нажмите **Добавить** , и Visual Studio создаст представление. После этого откроется созданный CSHTML-файл. Можно закрыть этот файл в Visual Studio, мы вернемся к нему позже.

#### <a name="AddNewIndexView"></a>Добавление представления «Создание элементов»
Точно так же, как мы создали представление **Индекс элементов**, теперь мы создадим новое представление для создания новых **элементов**.

1. В **обозревателе решений** снова щелкните правой кнопкой мыши папку **Элемент**, выберите **Добавить**, а затем щелкните **Представление**.
2. В диалоговом окне **Добавление представления** выполните следующие действия.
   
   * В поле **Имя представления** введите ***Создать***.
   * В поле **Шаблон** выберите ***Создать***.
   * В поле **Класс модели** выберите ***Item (todo.Models)*** (Элемент (todo.Models)).
   * Оставьте пустым поле **Класс контекста данных** .
   * В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
   * Щелкните **Добавить**.

#### <a name="_Toc395888515"></a>Добавление представления «Редактирование элементов»
И, наконец, добавим последнее представление для редактирования **элементов** , как мы делали это ранее.

1. В **обозревателе решений** снова щелкните правой кнопкой мыши папку **Элемент**, выберите **Добавить**, а затем щелкните **Представление**.
2. В диалоговом окне **Добавление представления** выполните следующие действия.
   
   * В поле **Имя представления** введите ***Изменить***.
   * В поле **Шаблон** выберите ***Изменить***.
   * В поле **Класс модели** выберите ***Item (todo.Models)*** (Элемент (todo.Models)).
   * Оставьте пустым поле **Класс контекста данных** . 
   * В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
   * Щелкните **Добавить**.

Как только все будет готово, закройте документы cshtml в Visual Studio, мы вернемся к этим представлениям позже.

## <a name="_Toc395637769"></a>Шаг 5. Подключение Azure Cosmos DB
Теперь, когда мы позаботились об основных ресурсах MVC, давайте рассмотрим добавление кода для Azure Cosmos DB. 

В этом разделе мы добавим код для обработки следующих команд

* [Вывод списка незавершенных элементов](#_Toc395637770).
* [Добавление элементов](#_Toc395637771).
* [Редактирование элементов](#_Toc395637772).

### <a name="_Toc395637770"></a>Отображение незавершенных элементов в веб-приложении MVC
Прежде всего следует добавить класс, который содержит всю необходимую логику для подключения к службе Azure Cosmos DB и ее использования. В этом учебнике мы оформим всю эту логику в класс репозитория с именем DocumentDBRepository. 

1. В **обозревателе решений** щелкните правой кнопкой мыши проект, выберите **Добавить**, а затем щелкните **Класс**. Назовите новый класс **DocumentDBRepository** и нажмите кнопку **Добавить**.
2. В только что созданном классе **DocumentDBRepository** добавьте следующие *операторы using* над объявлением *namespace*.
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
   
    Теперь замените этот код 
   
        public class DocumentDBRepository
        {
        }
   
    следующим кодом:
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
   > [!TIP]
   > При создании новой коллекции DocumentCollection можно указать необязательный параметр OfferType RequestOptions, который позволяет указать уровень производительности новой коллекции. Если этот параметр не передан, будет использоваться тип предложения по умолчанию. Дополнительные сведения о типах предложений Azure Cosmos DB см. в статье об [уровнях производительности в Azure Cosmos DB](performance-levels.md).
   > 
   > 
3. Некоторые значения будут считываться из конфигурации, поэтому нужно открыть файл **Web.config** и добавить следующие строки в разделе `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Теперь обновите значения для параметров *endpoint* и *authKey*, используя колонку "Ключи" на портале Azure. Используйте **URI** из колонки "Ключи" в качестве значения параметра endpoint. Что касается значения параметра authKey, то в этом качестве используйте значение **первичного ключа** или **вторичного** из колонки "Ключи".

    Это касается подключения репозитория DocumentDB. Теперь можно добавить логику приложения.

1. Первое, что мы хотим сделать с приложением списка заданий, — отображение незавершенных элементов.  Скопируйте и вставьте следующий фрагмент кода в любом месте в класса **DocumentDBRepository** .
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Откройте **ItemController** , который мы добавили ранее, и добавьте следующие *операторы using* перед декларацией namespace.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Если проект не называется todo, необходимо выполнить обновление с помощью todo.Models в соответствии с именем проекта.
   
    Теперь замените этот код
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    следующим кодом:
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Откройте файл **Global.asax.cs** и добавьте в метод **Application_Start** следующую строку: 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

На данном этапе ваше решение должно компилироваться без каких-либо ошибок.

Чтобы запустить приложение сейчас, нужно перейти к **главному контроллеру** и представлению **индекса** в этом контроллере. Это поведение по умолчанию для шаблона проекта MVC мы выбрали вначале, но нам нужно кое-что другое! Давайте изменим маршрутизацию в данном приложении MVC, чтобы изменить это поведение.

Откройте файл ***App\_Start\RouteConfig.cs***, найдите строку, начинающуюся с defaults:, и измените ее следующим образом:

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Теперь приложение ASP.NET MVC будет знать, что, если вы не указали значение в поле URL-адреса для управления поведением маршрутизации, вместо **главной страницы** нужно использовать **Элемент** в качестве контроллера, а пользовательский **индекс** — в качестве представления.

Теперь при запуске приложения оно будет вызывать **ItemController**, который вызывает класс репозитория и использует метод GetItems, чтобы возвратить все незавершенные элементы в представлении **Представления**\\**Элемент**\\**Индекс**. 

Если создать и запустить этот проект сейчас, отобразится примерно следующие данные.    

![Снимок экрана: веб-приложение "Список дел", созданное с помощью этого учебника](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Добавление элементов
Добавим несколько элементов в нашу базу данных, чтобы не смотреть на пустую сетку.

Добавим код в репозиторий Azure Cosmos DBRepository и ItemController, чтобы сохранить запись в Azure Cosmos DB.

1. Добавьте следующий метод в класс **DocumentDBRepository** .
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Этот метод просто принимает любой объект, переданный ему, и сохраняет его в DocumentDB.
2. Откройте файл ItemController.cs и добавьте следующий фрагмент кода в класс. Теперь приложение ASP.NET MVC будет знать, что делать с действием **Создать** . В этом случае просто выводится связанное представление Create.cshtml, которое было создано ранее.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Теперь нам нужно добавить еще немного кода в этом контроллере, который будет принимать данные из представления **Создание** .
3. Добавьте следующий блок кода в класс ItemController.cs, который указывает приложению ASP.NET MVC, что нужно делать с формой POST для этого контроллера.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Этот код вызывает DocumentDBRepository и использует метод CreateItemAsync для сохранения нового элемента списка дел в базе данных. 
   
    **Примечание по безопасности.** Атрибут **ValidateAntiForgeryToken** используется здесь для защиты этого приложения от атак с подделкой межсайтовых запросов. Кроме добавления этого атрибута представления также должны работать с данным маркером защиты от подделки запросов. Дополнительные сведения об этом и примеры правильной реализации этой технологии см. в записи блога [Предотвращение атак с подделкой межсайтовых запросов][Preventing Cross-Site Request Forgery]. Исходный код, предоставленный на [GitHub][GitHub], имеет полную реализацию.
   
    **Примечание по безопасности.** Для защиты от атак overposting также используется атрибут **Bind** в параметре метода. Дополнительные сведения см. в записи блога [Основные операции CRUD в ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

На этом мы завершаем написание кода, необходимого для добавления новых элементов в нашу базу данных.

### <a name="_Toc395637772"></a>Редактирование элементов
И последнее, необходимо обеспечить возможность изменить **Элементы** в базе данных и отмечать их как завершенные. Представление изменения уже добавлено в проект, поэтому просто нужно еще раз добавить дополнительный код в контроллер и класс **DocumentDBRepository**.

1. Добавьте следующие методы в класс **DocumentDBRepository** .
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    Первый из этих двух методов, **GetItem**, извлекает элемент из Azure Cosmos DB, который передается обратно в **ItemController**, а затем — в представление **Изменить**.
   
    Второй метод, который мы только что добавили, заменяет **документ** в Azure Cosmos DB версией **документа**, переданного из **ItemController**.
2. Добавьте следующие методы в класс **ItemController** .
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    Первый метод обрабатывает команду HTTP GET, формирующуюся, когда пользователь выбирает ссылку **Изменить** в представлении **Индекс**. Этот метод извлекает [**документ**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) из Azure Cosmos DB и передает его в представление **Изменить**.
   
    Представление **Изменить** отправляет команду HTTP POST в **IndexController**. 
   
    Второй метод, который мы добавили, обрабатывает передачу обновленного объекта в Azure Cosmos DB, чтобы сохранить его в базе данных.

Вот и все, что необходимо для запуска приложения, отображения списка незавершенных **элементов**, добавления новых **элементов** и изменения **элементов**.

## <a name="_Toc395637773"></a>Шаг 6. Локальный запуск приложения
Для проверки приложения на локальном компьютере выполните следующие действия.

1. Чтобы создать приложение в режиме отладки, откройте Visual Studio и нажмите клавишу F5. После этого будет создано приложение и откроется окно браузера с пустой сеткой, которую мы уже видели:
   
    ![Снимок экрана: веб-приложение "Список дел", созданное с помощью этого учебника](./media/documentdb-dotnet-application/image24.png)
   
    Если при использовании Visual Studio 2013 возникает сообщение об ошибке "Невозможно ожидание в теле блока catch", необходимо установить [пакет NuGet Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers/). Вы также можете сравнить ваш код с кодом примера проекта на [GitHub][GitHub]. 
2. Щелкните ссылку **Создать**, введите значения в поля **Имя** и **Описание**. Не устанавливайте флажок **Выполнено**, иначе новый **элемент** будет добавлен ​​в завершенном состоянии и не будет отображаться в начальном списке.
   
    ![Снимок экрана: представление "Создание"](./media/documentdb-dotnet-application/image25.png)
3. Щелкните **Создать**. Вы будете перенаправлены в представление **Индекс**, а в списке появится ваш **элемент**.
   
    ![Снимок экрана: представление "Индекс"](./media/documentdb-dotnet-application/image26.png)
   
    Вы можете добавить еще несколько **Элементов** в список задач.
4. Нажмите кнопку **Изменить** рядом с **Элемент**. Вы будете перенаправлены в представление **Изменить**, где сможете изменить любое свойство объекта, в том числе флажок **Выполнено**. Если установить флажок **Выполнено** и нажать кнопку **Сохранить**, **Элемент** будет удален из списка незавершенных задач.
   
    ![Снимок экрана: представление "Индекс" с установленным флажком возле параметра "Завершено"](./media/documentdb-dotnet-application/image27.png)
5. После проверки приложения нажмите клавиши CONTROL+F5, чтобы остановить отладку приложения. Теперь все готово к развертыванию.

## <a name="_Toc395637774"></a>Шаг 7. Развертывание приложения на веб-сайтах Azure
Теперь, когда у вас есть готовое приложение, которое корректно работает в Azure Cosmos DB, мы собираемся развернуть его на веб-сайтах Azure. Если вы выбрали вариант **Разместить в облаке** , после создания пустого проекта ASP.NETMVC Visual Studio упростит процедуру и сделает большую часть работы за нас. 

1. Чтобы опубликовать это приложение, щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать**.
   
    ![Снимок экрана: параметр "Публикация" в обозревателе решений](./media/documentdb-dotnet-application/image28.png)
2. Все уже должно быть настроено в соответствии с вашими учетными данными. На самом деле сайт уже создан в Azure и находится по **конечному URL-адресу**, вам просто нужно нажать кнопку **Опубликовать**.
   
    ![Снимок экрана: диалоговое окно "Публикация веб-сайта" в Visual Studio. Пошаговое руководство по ASP.NET MVC](./media/documentdb-dotnet-application/image29.png)

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

## <a name="Troubleshooting"></a>Устранение неполадок

Если при попытке развернуть веб-приложение возникнет ошибка An error occurred while processing your request ("Возникла ошибка в процессе обработки запроса"), выполните действия ниже: 

1. Нажмите кнопку отмены в сообщении об ошибке, а затем щелкните **Веб-приложения Microsoft Azure** еще раз. 
2. Войдите, а затем щелкните **Создать**, чтобы создать веб-приложение. 
3. На экране **Создание веб-приложения в Microsoft Azure** укажите следующее: 
    
    - "Имя веб-приложения" — todo-net-app.
    - "План службы приложений" — создайте новый план с именем todo-net-app.
    - "Группа ресурсов" — создайте группу с именем todo-net-app.
    - "Регион" — выберите ближайший к пользователям приложения регион.
    - "Сервер базы данных" — щелкните "Без базы данных", а затем **Создать**. 

4. На экране todo-net-app  **щелкните** Проверить подключение. После проверки подключения щелкните **Опубликовать***. 
    
    Затем приложение отобразится в браузере.


## <a name="_Toc395637775"></a>Дальнейшие действия
Поздравляем! Вы только что создали свое первое веб-приложение ASP.NET MVC с помощью Azure Cosmos DB и опубликовали его на веб-сайтах Azure. Исходный код полного приложения, включая функции детализации и удаления, не описанные в этом руководстве, можно скачать или клонировать с портала [GitHub][GitHub]. Поэтому, если вы хотите добавить эти функции в приложение, скопируйте код и добавьте его в это приложение.

Чтобы добавить дополнительные функции в приложение, ознакомьтесь с интерфейсами API, доступными в [библиотеке DocumentDB .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet). Вы также можете быть соавтором библиотеки DocumentDB .NET на портале [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app

