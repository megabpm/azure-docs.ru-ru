---
title: "Основные сведения об Apache Storm в Azure HDInsight | Документация Майкрософт"
description: "Apache Storm позволяет обрабатывать потоки данных в режиме реального времени. Azure HDInsight позволяет легко создавать кластеры Storm в облаке Azure. С помощью Visual Studio можно создать решения Storm, используя C#, а затем развернуть их в кластерах HDInsight Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: "варианты использования apache storm, кластер storm, сведения об apache storm"
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/12/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 9c17268d775d0c54468a38a85c746d624f449a9d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Основные сведения об Apache Storm в Azure HDInsight

[Apache Storm](http://storm.apache.org/) — это распределенная отказоустойчивая вычислительная система с открытым исходным кодом. Вы можете использовать кластер Storm для обработки потоков данных в режиме реального времени с помощью Hadoop. Решения Storm могут также обеспечить гарантированную обработку данных и возможность воспроизвести те данные, которые не прошли удачную обработку в первый раз.

Использование Storm в HDInsight обеспечивает следующие преимущества:

* Решение выполняется как управляемая служба с доступностью в течение 99,9 % времени.

* Простая настройка с помощью скриптов во время или после создания кластера Storm. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

* Использование различных языков. Вы можете написать компоненты Storm на удобном для вас языке, например Java, C# и Python.

    * Интеграция Visual Studio с HDInsight для разработки, администрирования и отслеживания топологий C#. Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Поддержка Java-интерфейса Trident. Вы можете создавать топологии Storm, поддерживающие обработку сообщений в рамках подхода "только один раз", сохраняемость "транзакционных" хранилищ данных, а также набор распространенных операций Stream Analytics.

*  Простое масштабирование кластеров Storm. Вы можете добавить или удалить рабочие узлы, не влияя на выполняющиеся топологии Storm.

* Интеграция со следующими службами Azure:

    * Концентраторы событий Azure

    * Виртуальная сеть Azure

    * База данных SQL Azure

    * Хранилище Azure

    * Azure Cosmos DB

* Объединение возможностей нескольких кластеров HDInsight с помощью виртуальной сети Azure. Вы можете создавать конвейеры аналитики, использующие кластеры Storm, Kafka, Spark, HBase или Hadoop.

Список компаний, использующих Apache Storm в качестве решения для анализа данных в реальном времени, см. на [этой странице](https://storm.apache.org/documentation/Powered-By.html).

Сведения для начала работы с системой Storm см. в статье [Начало работы со Storm в HDInsight][gettingstarted].

## <a name="how-does-storm-work"></a>Как работает Storm

Storm работает с топологиями, а не заданиями MapReduce, которые могут быть вам знакомы. Топологии Storm состоят из нескольких компонентов, которые расположены в виде направленного ациклического графа (DAG). На указанной ниже схеме показаны потоки данных между компонентами. Каждый компонент использует один или несколько потоков данных и может генерировать дополнительные потоки. На следующей схеме показан поток данных между компонентами в базовой топологии подсчета слов:

![Пример расположения компонентов в топологии Storm](./media/hdinsight-storm-overview/example-apache-storm-topology-diagram.png)

* Компоненты spout переносят данные в топологию. Они генерируют один или несколько потоков в топологии.

* Компоненты bolt используют потоки, генерируемые элементами spout или другими элементами bolt. Элементы bolt могут дополнительно генерировать потоки в топологии. Они также отвечают за запись данных во внешние службы или хранилище, например HDFS, Kafka или HBase.

## <a name="ease-of-creation"></a>Простота создания

Новый кластер Storm в HDInsight можно подготовить за считаные минуты. Сведения о создании кластера Storm см. в статье [Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Простота использования

* __Подключение Secure Shell.__ Головные узлы кластера Storm доступны через Интернет по протоколу SSH. С помощью SSH вы можете выполнять команды непосредственно в кластере.

  Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Веб-подключение.__ Все кластеры HDInsight предоставляют веб-интерфейс Ambari. Он позволяет легко отслеживать, настраивать и администрировать службы в кластере. Кластеры Storm также предоставляют пользовательский интерфейс Storm. С его помощью вы можете отслеживать и контролировать выполнение топологий Storm из браузера.

  Дополнительные сведения см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md) и разделе [Мониторинг и управление: пользовательский интерфейс Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell и Azure CLI.__ Обе эти служебные программы командной строки можно использовать для работы с HDInsight и другими службами Azure из клиентской системы.

* __Интеграция с Visual Studio.__ Средства Azure Data Lake для Visual Studio включают шаблоны проектов для создания топологий C# Storm с помощью платформы SCP.Net. Средства Data Lake также предоставляют средства для развертывания, отслеживания и администрирования решений с помощью Storm в HDInsight.

  Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Интеграция с другими службами Azure

* __Azure Data Lake Store.__ Примеры использования Data Lake Store с помощью кластера Storm см. в статье [Использование Azure Data Lake Store с помощью Apache Storm в HDInsight (Java)](hdinsight-storm-write-data-lake-store.md).

* __Концентраторы событий.__ Примеры использования концентраторов событий с кластером Storm см. в следующих документах:

    * [Разработка топологии подсчета слов на основе Java для Storm в HDInsight с помощью Maven](hdinsight-storm-develop-java-topology.md)

    * [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* __База данных SQL__, __Cosmos DB__, __концентраторы событий__ и __HBase.__ Примеры шаблонов для работы с ними включены в средства Data Lake для Visual Studio. Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Надежность

Система Apache Storm гарантирует, что каждое входящее сообщение будет полностью обработано даже в том случае, когда анализ данных распределен между сотнями узлов.

Функциональные возможности узла Nimbus схожи с возможностями службы Hadoop JobTracker. Через Zookeeper этот узел назначает задачи другим узлам в кластере. Узлы Zookeeper обеспечивают координацию кластера и упрощают взаимодействие между узлом Nimbus и процессом контролера на рабочих узлах. Если один узел обработки выходит из строя, узел Nimbus узнает об этом и назначает задачу и связанные данные другому узлу.

По умолчанию для кластеров Apache Storm используется только один узел Nimbus, но кластер Storm в HDInsight имеет два. При сбое основного узла кластер Storm переключится на дополнительный на время восстановления основного узла. На схеме ниже показана конфигурация последовательности задач для Storm в HDInsight.

![Схема с контролерами и компонентами Nimbus и Zookeeper](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Масштаб

Вы можете выполнить динамическое масштабирование кластеров HDInsight, добавив или удалив рабочие узлы, во время обработки данных.

> [!IMPORTANT]
> Чтобы воспользоваться преимуществами новых узлов, добавленных с помощью масштабирования, необходимо повторно сбалансировать топологии Storm, запущенные до увеличения размера кластера.

## <a name="support"></a>Поддержка

Для кластеров Storm в HDInsight действует полная поддержка корпоративного уровня. Кроме того, для кластеров Storm в HDInsight заявлена гарантированная доступность в течение 99,9 % времени. Это означает, что мы гарантируем возможность подключения к кластеру Storm извне в течение как минимум 99,9 % времени.

Дополнительные сведения см. на странице [службы поддержки Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Варианты использования Apache Storm

Ниже приведены несколько распространенных ситуаций, в которых вам может помочь кластер Storm в HDInsight.

* Интернет вещей.
* Обнаружение мошенничества.
* Социальная аналитика.
* Извлечение, преобразование и загрузка.
* Мониторинг сетей.
* Поиск
* Взаимодействие через мобильные устройства.

Сценарии из реальной жизни см. в статье о том, [как компании используют Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Разработка

Средства Data Lake для Visual Studio позволяют разработчикам .NET проектировать и реализовывать топологии на языке C#. Вы также можете создавать гибридные топологии, в которых используются компоненты Java и C#.

Дополнительные сведения см. в статье [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Можно также разрабатывать решения Java с помощью интегрированной среды разработки по своему усмотрению. Дополнительные сведения см. в статье [Разработка топологии подсчета слов на основе Java для Storm в HDInsight с помощью Maven](hdinsight-storm-develop-java-topology.md).

Для разработки компонентов Storm также может использоваться Python. Дополнительные сведения см. в статье [Разработка топологий Apache Storm с помощью Python в HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Общие шаблоны разработки

### <a name="guaranteed-message-processing"></a>Гарантированная обработка сообщений

Apache Storm может обеспечить различные уровни гарантированной обработки сообщений. Например, простое приложение Storm гарантирует как минимум одну обработку, в то время как Trident может гарантировать ровно одну обработку.

Дополнительные сведения см. в статье о [гарантированной обработке данных](https://storm.apache.org/about/guarantees-data-processing.html) на сайте apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Шаблон чтения входного кортежа, выдающий значение 0 или больше, а затем непосредственно в конце метод execute, запрашивающий подтверждение входного кортежа, является типичным. Storm обеспечивает интерфейс [IBasicBolt](https://storm.apache.org/releases/1.0.3/javadocs/org/apache/storm/topology/IBasicBolt.html) для автоматизации этого шаблона.

### <a name="joins"></a>Соединения

Объединение потоков данных отличается в разных приложениях. Например, вы можете объединять все кортежи с нескольких потоков в один новый поток или объединять только пакеты кортежей для отдельного окна. В любом случае объединение можно выполнить с помощью [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Группирование полей — это способ определения того, как кортежи перенаправляются к элементам bolts.

В приведенном ниже примере Java используется fieldsGrouping для перенаправления кортежей из компонентов "1", "2" и "3" в элемент bolt MyJoiner.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Пакеты

Apache Storm предоставляет внутренний временной механизм (временный кортеж). Вы можете задать частоту использования временного кортежа в топологии.

Пример использования временного кортежа из компонента на C# см. здесь: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Кэши

Для ускорения обработки часто используется кэширование в памяти, при котором в памяти сохраняются часто используемые ресурсы. Так как топология распределяется между несколькими узлами и несколькими процессами в пределах одного узла, вы можете использовать [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). `fieldsGrouping` гарантирует, что кортежи, содержащие поля, используемые для поиска в кэше, всегда перенаправляются к одному и тому же процессу. Эта функция группирования избавит от дублирования записей кэша в процессах.

### <a name="stream-top-n"></a>Передача значения "Первые N"

Когда ваша топология зависит от расчета значения "Первые N", это значение нужно рассчитывать параллельно. Затем объедините результат расчетов в глобальное значение. Эту операцию можно выполнить с помощью [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-), направив отдельные поля в параллельную обработку, а затем — в элемент bolt, который определяет глобальное значение "Топ N".

Для расчета значения "Первые N" см. пример [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Ведение журналов

Storm использует Apache Log4j для записи информации в журнал. По умолчанию в журнал записывается большой объем данных, и разобраться в этой информации может быть трудно. Чтобы управлять ведением журналов, включите файл конфигурации ведения журналов в топологию Storm.

Пример топологии, в котором показано, как настроить ведение журнала, см. в примере Storm в HDInsight в статье [Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight](hdinsight-storm-develop-java-topology.md).

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены статьи, в которых можно найти дополнительные сведения о решениях для анализа данных в реальном времени с помощью Storm в HDInsight.

* [Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux][gettingstarted]
* [Примеры топологий и компонентов Storm для Apache Storm в HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

