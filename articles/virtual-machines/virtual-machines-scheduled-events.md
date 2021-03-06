---
title: "Запланированные события в службе метаданных Azure | Документация Майкрософт"
description: "Упреждающее реагирование на важные события на виртуальной машине."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 627aa117ded0aaa519052d4ea1a1995ba2e363ee
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Служба метаданных Azure. Запланированные события (предварительная версия)

> [!NOTE] 
> Предварительные версии предоставляются при условии, что вы соглашаетесь с условиями использования. Дополнительные сведения см. в разделе [Дополнительные условия использования Предварительных выпусков Microsoft Azure] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).
>

Запланированные события — одна из подслужб в службе метаданных Azure, которая предоставляет сведения о предстоящих событиях (например, перезагрузках) позволяет приложению подготовиться к ним и уменьшить влияние на работу. Эти сведения доступны для всех типов виртуальных машин Azure, включая PaaS и IaaS. Благодаря запланированным событиям виртуальная машина получает возможность выполнить упреждающие действия и свести влияние события к минимуму. 


## <a name="introduction---why-scheduled-events"></a>Введение. Зачем использовать запланированные события?

Запланированные события позволяют предпринять действия по ограничению воздействия на вашу службу. Рабочие нагрузки с несколькими экземплярами, которые используют методы репликации для поддержания состояния, чувствительны к частым простоям на нескольких экземплярах. Такие простои могут потребовать дорогостоящих действий (например, перестроения индексов) или привести к потере реплики. Во многих других случаях корректное завершение работы просто повышает общую доступность службы. Например, можно спокойно завершить или отменить текущие транзакции, переназначить задачи на другие виртуальные машины в кластере (ручная отработка отказа) или удалить виртуальную машину из пула подсистемы балансировки нагрузки. Иногда простое уведомление администратора о предстоящих событиях или даже занесение события в журнал позволяет повысить удобство обслуживания приложений, размещенных в облаке.
Служба метаданных Azure сообщает о запланированных событиях в следующих случаях.
-    Инициируемое платформой обслуживание (например, развертывание ОС узла).
-    Инициируемые пользователем вызовы (например, пользователь перезапускает или повторно развертывает виртуальную машину).


## <a name="scheduled-events---the-basics"></a>Запланированные события. Основы  

Служба метаданных Azure предоставляет сведения о работающих виртуальных машинах через конечную точку REST на самой виртуальной машине. Эта информация предоставляется через немаршрутизируемый IP-адрес, то есть она недоступна вне виртуальной машины.

### <a name="scope"></a>Область
Запланированные события отображаются на всех виртуальных машинах в облачной службе или на всех виртуальных машинах в группе доступности. Поэтому важно проверять поле **Resources** в событии, чтобы определить, на какие виртуальные машины повлияет конкретное событие. 

### <a name="discover-the-endpoint"></a>Обнаружение конечной точки
В случае, когда виртуальная машина создается в виртуальной сети, служба метаданных доступна по немаршрутизируемому IP-адресу 169.254.169.254. В противном случае (по умолчанию для облачных служб и классических виртуальных машин) требуется настроить дополнительную логику для обнаружения используемой конечной точки. Чтобы узнать, как это сделать, ознакомьтесь с примером [обнаружения конечной точки узла] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Управление версиями 
Для службы метаданных экземпляров включено управление версиями. Версии являются обязательными. На данный момент используется версия от 01.03.2017.

> [!NOTE] 
> В предыдущих выпусках предварительной версии в качестве api-version поддерживалось значение {latest}. Этот формат больше не поддерживается и в дальнейшем будет считаться устаревшим.
>


### <a name="using-headers"></a>Использование заголовков
В запросе к службе метаданных необходимо указать следующий заголовок: *Metadata: true*. 

### <a name="enable-scheduled-events"></a>Активация запланированных событий
При первом вызове информации о запланированных событиях Azure неявно включает эту функцию на виртуальной машине. Это означает, что при первом вызове возможна задержка с ответом длительностью до двух минут.

### <a name="testing-your-logic-with-user-initiated-operations"></a>Тестирование логики с помощью инициируемых пользователем операций
Чтобы протестировать логику, можно использовать портал Azure, API, интерфейс командной строки или PowerShell для запуска операций, приводящих к возникновению запланированных событий. Перезапуск виртуальной машины приводит к созданию запланированного события с типом Reboot. Повторное развертывание виртуальной машины приводит к созданию запланированного события с типом Redeploy.
В обоих случаях инициируемая пользователем операция занимает больше времени, так как запланированные события предоставляют больше времени для корректного завершения работы приложения. 

## <a name="using-the-api"></a>Использование API

### <a name="query-for-events"></a>Запрос сведений о событиях
Чтобы получить сведения о запланированных событиях, достаточно отправить следующий запрос:

    curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01


Ответ содержит массив запланированных событий. Если это будет пустой массив, значит сейчас запланированных событий нет.
Если передаются запланированные события, массив событий в ответе выглядит так: 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }
    
### <a name="event-properties"></a>Свойства события
|Свойство  |  Описание |
| - | - |
| EventId |Глобальный уникальный идентификатор события. <br><br> Пример: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Влияние, которое оказывает событие. <br><br> Значения: <br><ul><li> <i>Freeze.</i> Виртуальная машина будет приостановлена на несколько секунд. Это действие не повлияет на память, открытые файлы и сетевые подключения. <li> <i>Reboot.</i> Виртуальная машина будет перезагружена с очисткой памяти.<li> <i>Redeploy.</i> Виртуальная машина будет перемещена на другой узел с потерей данных на временных дисках. |
| ResourceType | Тип ресурса, на который влияет событие. <br><br> Значения: <ul><li>VirtualMachine|
| Ресурсы| Список ресурсов, на которые влияет событие. <br><br> Пример: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Event Status | Состояние события. <br><br> Значения: <ul><li><i>Scheduled.</i> Запланированное событие состоится по истечении времени, указанного в свойстве <i>NotBefore</i>.<li><i>Started.</i> Событие запущено.</i>
| NotBefore| Время, после которого будет запущено событие. <br><br> Пример: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Планирование события
В зависимости от типа каждое будущее событие будет выполняться минимальное количество времени. Это время отражается в свойстве события <i>NotBefore</i>. 

|EventType  | Минимальное время уведомления |
| - | - |
| Freeze| 15 минут |
| Reboot | 15 минут |
| Повторное развертывание | 10 минут |

### <a name="starting-an-event-expedite"></a>Запуск (ускорение) события

Когда вы узнаете о предстоящем событии и завершите все действия для корректного завершения работы, с помощью вызова **POST** можно сообщить Azure, что событие можно выполнить раньше (если это возможно). 
 

## <a name="powershell-sample"></a>Пример для PowerShell 

В следующем примере считываются метаданные сервера для запланированных событий, затем эти события утверждаются.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>Пример на языке C\# 
Ниже приведен пример клиентских интерфейсов API подключения для взаимодействия со службой метаданных.
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
Запланированные события можно анализировать с использованием следующих структур данных: 

```csharp
    public class ScheduledEventsDocument
    {
        public string DocumentIncarnation;
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime? NotBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public string DocumentIncarnation;
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

Пример программы, которая использует этот клиент для получения, обработки и подтверждения событий:   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
        {
            DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
        };
        
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Пример на Python 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>Дальнейшие действия 
[Плановое обслуживание виртуальных машин Linux](linux/planned-maintenance.md)
[Предварительная версия службы метаданных экземпляров Azure](virtual-machines-instancemetadataservice-overview.md)

