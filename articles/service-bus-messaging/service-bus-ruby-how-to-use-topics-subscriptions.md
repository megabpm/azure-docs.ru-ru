---
title: "Использование разделов служебной шины (Ruby) | Документация Майкрософт"
description: "Узнайте, как использовать разделы и подписки служебной шины в Azure. Примеры кода написаны для приложений Ruby."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 3a401be134d034ee6ed4c88df4ed728b727894c0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-use-service-bus-topicssubscriptions"></a>Использование разделов и подписок служебной шины
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этой статье показано, как использовать разделы и подписки служебной шины в приложениях Ruby. В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений из подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Создание раздела
Объект **Azure::ServiceBusService** позволяет работать с разделами. Следующий код создает объект **Azure::ServiceBusService**. Чтобы создать раздел, используйте метод **create\_topic()**. В следующем примере создается раздел или выводятся возникающие ошибки.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Также можно передать объект **Azure::ServiceBus::Topic** с дополнительными параметрами, которые позволяют переопределить параметры раздела по умолчанию, например срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни 1 минута.

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Создание подписок
Подписки разделов также создаются с помощью объекта **Azure::ServiceBusService**. Подписки имеют имена и могут использовать дополнительный фильтр, который ограничивает набор сообщений, доставляемых в виртуальную очередь подписки.

Подписки являются постоянными и продолжают существовать либо до их удаления, либо до удаления раздела, с которым они связаны. Если приложение содержит логику для создания подписки, оно сначала должно проверить, существует ли подписка, используя метод getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Создание подписки с фильтром по умолчанию (MatchAll)
Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, то все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "all-messages" и используется фильтр по умолчанию **MatchAll**.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Создание подписок с фильтрами
Можно также задавать фильтры, позволяющие определять, какие посылаемые в раздел сообщения должны отображаться в рамках конкретной подписки.

Самый гибкий тип фильтра, который поддерживается подписками, — это **Azure::ServiceBus::SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса [SqlFilter](service-bus-messaging-sql-filter.md).

Фильтры можно добавить в подписку с помощью метода **create\_rule()** объекта **Azure::ServiceBusService** . Этот метод позволяет добавлять новые фильтры в существующую подписку.

Так как ко всем новым подпискам автоматически применяется фильтр по умолчанию, сначала необходимо удалить фильтр по умолчанию, иначе **MatchAll** будет действовать вместо любых других заданных фильтров. Вы можете удалить правило по умолчанию с помощью метода **delete\_rule()** объекта **Azure::ServiceBusService**.

В приведенном ниже примере создается подписка с именем high-messages с фильтром **Azure::ServiceBus::SqlFilter**, который выбирает только те сообщения, значение настраиваемого свойства **message\_number** которых больше 3.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

В следующем примере создается подписка с именем low-messages с фильтром **Azure::ServiceBus::SqlFilter**, выбирающим только те сообщения, значение свойства **message_number** которых меньше или равно 3.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Если сообщение отправляется в раздел test-topic, оно всегда доставляется в приемники с подпиской раздела all-messages, а также в отдельные приемники с подписками разделов high-messages и low-messages (в зависимости от содержимого сообщений).

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Чтобы отправить сообщение в раздел служебной шины, приложение должно использовать метод **send\_topic\_message()** в объекте **Azure::ServiceBusService**. Сообщения, отправленные в разделы служебной шины, представляют собой экземпляры объектов **Azure::ServiceBus::BrokeredMessage**. У объектов **Azure::ServiceBus::BrokeredMessage** есть набор стандартных свойств (например, **label** и **time\_to\_live**), словарь, используемый для хранения настраиваемых свойств приложения и набор строковых данных. Приложение может задавать текст сообщения, передавая строковое значение в метод **send\_topic\_message()**, и все обязательные стандартные свойства будут заполнены значениями по умолчанию.

В следующем примере показано, как отправить пять тестовых сообщений в раздел test-topic. Обратите внимание, что значение настраиваемого свойства **message_number** каждого сообщения зависит от итерации цикла (это определяет подписку, которая получит это сообщение).

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
Сообщения извлекаются из подписки с помощью метода **receive\_subscription\_message()** объекта **Azure::ServiceBusService**. По умолчанию сообщения считываются (просматриваются) и блокируются без удаления из подписки. Вы можете читать сообщения и удалять их из подписки, установив для параметра **peek\_lock** значение **false**.

По умолчанию чтение и удаление выполняются в два этапа, что позволяет поддерживать приложения, не способные обрабатывать отсутствующие сообщения. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **delete\_subscription\_message()** и указывая сообщение для удаления в качестве параметра. Метод **delete\_subscription\_message()** помечает сообщение как прочитанное и удаляет его из подписки.

Если параметр **:peek\_lock** имеет значение **false**, чтение и удаление сообщения осуществляется очень просто. Этот метод оптимально подходит для сценариев, в которых приложение может пропустить обработку сообщения без последствий в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В следующем примере показаны получение и обработка сообщений с помощью метода **receive\_subscription**message()\_. В этом примере сначала получается и удаляется сообщение из подписки low-messages (для этого параметру **:peek\_lock** присваивается значение **false**), а затем получается другое сообщение из high-messages, которое удаляется с помощью метода **delete\_subscription\_message()**.

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Обработка сбоев приложения и нечитаемых сообщений
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **unlock\_subscription\_message()** объекта **Azure::ServiceBusService**. После этого служебная шина разблокирует сообщение в подписке и снова сделает его доступным для получения тем же или другим приложением.

Кроме того, с сообщением, заблокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина автоматически разблокирует сообщение и сделает его доступным для повторного приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **delete\_subscription\_message()**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют *обработать хотя бы один раз*, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это реализуется с помощью свойства **message\_id** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="delete-topics-and-subscriptions"></a>Удаление разделов и подписок
Разделы и подписки хранятся постоянно, и их нужно удалять явным образом на [портале Azure][Azure portal] или с помощью программных средств. В приведенном ниже примере показано, как удалить раздел с именем test-topic.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем high-messages из раздела test-topic:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Дальнейшие действия
Вы узнали основные сведения о разделах служебной шины. Для получения дополнительных сведений используйте следующие ссылки.

* См. статью [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md).
* Справочник API для [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Посетите репозиторий [Azure SDK для Ruby](https://github.com/Azure/azure-sdk-for-ruby) на веб-сайте GitHub.

[Azure portal]: https://portal.azure.com

