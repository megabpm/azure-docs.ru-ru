---
title: "Как создать обработчик мультимедиа | Документация Майкрософт"
description: "Узнайте, как создать компонент обработчика мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 88f6e1da090eb6088e54c6f81d0f83b1737d3c2c
ms.lasthandoff: 04/12/2017


---
# <a name="how-to-get-a-media-processor-instance"></a>Получение экземпляра процессора мультимедиа
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Обзор
В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

| Имя обработчика мультимедиа | Описание | Дополнительные сведения |
| --- | --- | --- |
| Стандартный кодировщик служб мультимедиа |Предоставляет стандартные возможности для кодирования по запросу. |[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md) |
| Расширенный рабочий процесс кодировщика мультимедиа |Позволяет выполнять задачи кодирования с использованием расширенного рабочего процесса кодировщика мультимедиа. |[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md) |
| Azure Media Indexer |Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров. |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (предварительная версия) |Позволяет сгладить "неровности" видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Кодировщик мультимедиа Azure |Не рекомендуется | |
| Расшифровка хранилища |Не рекомендуется | |
| Azure Media Packager |Не рекомендуется | |
| Azure Media Encryptor |Не рекомендуется | |

## <a name="get-media-processor"></a>Получение обработчика мультимедиа
Приведенные ниже методы показывают, как получить экземпляр обработчика мультимедиа. В примере кода предполагается использование переменной уровня модуля с именем **_context** для ссылки на контекст сервера (см. статью [Подключение к учетной записи служб мультимедиа с помощью пакета SDK служб мультимедиа для .NET](media-services-dotnet-connect-programmatically.md)).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива](media-services-dotnet-encode-with-media-encoder-standard.md) , в котором будет показано, как использовать Media Encoder Standard для кодирования ресурса-контейнера.


