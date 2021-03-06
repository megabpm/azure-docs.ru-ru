---
title: "Аварийное восстановление в Azure Service Fabric | Документация Майкрософт"
description: "Azure Service Fabric предлагает возможности, необходимые для устранения всех типов сбоев. В этой статье описаны типы сбоев, которые могут возникать, и приведены способы их устранения."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/01/2017
ms.author: masnider
ms.translationtype: Human Translation
ms.sourcegitcommit: 6d8f489ac053db4898741671df73b6abfabeb0dd
ms.openlocfilehash: 73f5413fb7dd0ca179bf5012478a453963e996a1
ms.contentlocale: ru-ru
ms.lasthandoff: 12/14/2016


---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Аварийное восстановление в Azure Service Fabric
Важной частью доставки высокодоступного облачного приложения является гарантия его устойчивости к различного типа сбоям, включая те, которые вам неподвластны. В этой статье описывается физическая структура кластера Azure Service Fabric в контексте потенциальных сбоев и даются указания, как поступать в случае сбоев, чтобы ограничить или полностью исключить риск простоев или потери данных.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Физическая структура кластеров Service Fabric в Azure
Чтобы понять риски, связанные с различными типами сбоев, полезно знать принцип физического расположения кластеров в Azure.

При создании кластера Service Fabric в Azure необходимо выбрать регион для его размещения. Затем инфраструктура Azure подготовит ресурсы — в первую очередь количество запрошенных виртуальных машин — для этого кластера в регионе. Рассмотрим способ и место подготовки этих ВМ более подробно.

### <a name="fault-domains"></a>Домены сбоя
По умолчанию виртуальные машины в кластере равномерно распределены в логических группах, называемых доменами сбоя. Сегментирование выполняется на основании потенциальных сбоев оборудования узла. В частности, если две виртуальные машины находятся в двух разных доменах сбоя, можно быть уверенным, что они не используют общий источник питания или сетевой коммутатор. В результате сбой локальной сети или отключение питания, влияющие на одну виртуальную машину, не затронут другую, что позволит Service Fabric повторно сбалансировать нагрузку переставшей отвечать на запросы машины в кластере.

Для визуализации структуры кластера в доменах сбоя можно использовать схему кластера в [обозревателе Service Fabric](service-fabric-visualizing-your-cluster.md).

![Узлы, распределенные между доменами сбоев в обозревателе Service Fabric][sfx-cluster-map]

> [!NOTE]
> На другой оси на схеме кластера отображаются домены обновления, которые логически группируют узлы на основе запланированных мероприятий обслуживания. Кластеры Service Fabric в Azure всегда располагаются в пяти доменах обновления.
> 
> 

### <a name="geographic-distribution"></a>Географическое распределение
Сейчас доступно [30 регионов Azure во всем мире][azure-regions]. Кроме того, объявлено еще о нескольких регионах. В зависимости от спроса и доступности подходящих расположений в отдельном регионе может находиться один или несколько физических центров обработки данных. Однако следует отметить, что даже в регионах с несколькими физическими центрами обработки нельзя гарантировать равномерное распределение виртуальных машин кластера. В настоящее время все виртуальные машины для заданного кластера проходят подготовку в рамках одного физического расположения.

## <a name="dealing-with-failures"></a>Устранение сбоев
Существует несколько типов сбоев, которые могут оказать влияние на кластер. К каждому сбою применяются отдельные действия по устранению. Мы рассмотрим их в порядке вероятности возникновения.

### <a name="individual-machine-failures"></a>Сбои отдельных машин
Как уже упоминалось, сами по себе сбои отдельных машин, либо в виртуальной машине, либо в оборудовании или программном обеспечении ее размещения в домене сбоя, не представляют угрозы. Как правило, Service Fabric обнаруживает сбой в течение нескольких секунд и, учитывая состояние кластера, соответствующим образом реагирует на него. Например, если на узле были размещены первичные реплики секции, то новая первичная реплика будет выбрана из вторичных реплик секции. После того как Azure вернет машину в работоспособное состояние, она автоматически повторно подключится к кластеру и снова возьмет на себя часть рабочей нагрузки.

### <a name="multiple-concurrent-machine-failures"></a>Одновременные сбои нескольких машин
Несмотря на то, что домены сбоя значительно снижают риск одновременных сбоев машины, всегда существует вероятность вывода из строя одновременно нескольких машин в кластере в результате нескольких случайных сбоев.

Как правило, пока большая часть узлов остается доступной, кластер будет продолжать работу (хотя и с меньшей мощностью), поскольку реплики с отслеживанием состояния группируются в меньший набор машин, и для распределения нагрузки доступно меньше экземпляров без отслеживания состояния.

#### <a name="quorum-loss"></a>Потеря кворума
Если большая часть реплик секции службы с отслеживанием состояния выходит из строя, секция переходит в состояние, называемое "потерей кворума". На этом этапе Service Fabric запретит выполнение операций записи с этой секцией, чтобы сохранить согласованность и стабильность ее состояния. По сути, мы принимаем решение активировать период недоступности, чтобы клиенты не получали сообщения о том, что их данные были сохранены, хотя на самом деле это не так. Обратите внимание, что если вы разрешаете операции чтения из вторичных реплик для этой службы с отслеживанием состояния, вы можете продолжать выполнять эти операции в данном состоянии. Секция будет оставаться в состоянии потери кворума, пока не будет доступно достаточное количество реплик или пока администратор кластера не переведет систему в рабочий режим с помощью [API Repair-ServiceFabricPartition][repair-partition-ps].

> [!WARNING]
> Выполнение восстановления при неработающей первичной реплике приведет к потере данных.
> 
> 

Системные службы также могут испытывать потерю кворума, причем серьезность влияния зависит от конкретной службы. Например, потеря кворума в службе именования повлияет на разрешение имен, тогда как потеря кворума в службе диспетчера отработки отказа будет блокировать создание службы и переход на другой ресурс. Обратите внимание, что в отличие от ваших служб системные службы восстанавливать *не* рекомендуется. Просто дождитесь возврата реплик в работоспособное состояние.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Минимизация риска потери кворума
Чтобы свести к минимуму риск потери кворума, можно увеличить размер целевого набора реплик службы. Рекомендуется подумать о количестве реплик, необходимых с точки зрения количества недоступных узлов, которое вы можете себе позволить и которые остаются доступными для записи. При этом следует учитывать, что наряду со сбоями оборудования обновления приложения или кластера могут привести к временной недоступности узлов.

В следующих примерах предположим, что вы настроили службы, задав параметру MinReplicaSetSize значение "3" — наименьшее число, которое рекомендуется использовать для производственных служб. Если параметру TargetReplicaSetSize задано значение "3" (одна первичная и две вторичных), сбой оборудования во время обновления (нарушение работоспособности двух реплик) приведет к потере кворума и служба станет доступной только для чтения. Кроме того, при наличии пяти реплик вы сможете выдержать два сбоя во время обновления (нарушение работоспособности трех реплик), так как оставшиеся две реплики могут по-прежнему формировать кворум в минимальном наборе реплик.

### <a name="data-center-outages-or-destruction"></a>Простои или разрушение центра обработки данных
В редких случаях физические центры обработки данных могут стать временно недоступными из-за отключения питания или потери сетевого подключения. При этом кластеры и приложения Service Fabric также будут недоступны, однако ваши данные сохранятся. Просмотреть обновления сбоев для кластеров, запущенных в Azure, можно на [странице состояния Azure][azure-status-dashboard].

В крайне маловероятном случае разрушения всего физического центра обработки данных будут потеряны все размещенные в нем кластеры Service Fabric, а также их состояние.

Чтобы избежать такой ситуации, очень важно регулярно [создавать резервные копии состояния](service-fabric-reliable-services-backup-restore.md) в геоизбыточном хранилище, обеспечивая возможность его восстановления. Частота резервного копирования будет зависеть от вашей целевой точки восстановления (RPO). Даже если вы пока не полностью реализовали стратегию архивации и восстановления, необходимо реализовать обработчик для события `OnDataLoss` , чтобы вы смогли записать возникшее событие, как показано далее.

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(false);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Сбои программного обеспечения и другие источники потери данных
Дефекты кода в службах, возникновение ошибок пользователя являются более распространенной причиной потери данных, чем массовые сбои в центрах обработки данных. Однако во всех случаях действует одинаковая стратегия восстановления: регулярное создание резервных копий всех служб с отслеживанием состояния и реализация возможности восстановления состояния.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как моделировать различные сбои с помощью [платформы тестирования](service-fabric-testability-overview.md)
* Ознакомьтесь с другими материалами по аварийному восстановлению и обеспечению высокой доступности. Корпорация Майкрософт опубликовала множество руководств по этим темам. Несмотря на то, что некоторые из этих документов посвящены конкретным методам для других продуктов, они содержат целый ряд общих практических рекомендаций, которые можно применять в контексте Service Fabric.
  * [Контрольный список для обеспечения доступности](../best-practices-availability-checklist.md)
  * [Отработка аварийного восстановления](../sql-database/sql-database-disaster-recovery-drills.md)
  * [Аварийное восстановление и высокий уровень доступности для приложений Azure][dr-ha-guide]
* Узнайте о [вариантах поддержки Service Fabric](service-fabric-support.md).

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

