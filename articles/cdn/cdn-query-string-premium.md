---
title: "Управление режимом кэширования Azure CDN с помощью строк запросов (ценовая категория &quot;Премиум&quot;) | Документация Майкрософт"
description: "Функция кэширования строк запроса Azure CDN управляет способом кэширования файлов, содержащих строки запроса."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 57d00f2192fed7a2e89ac94e110ebb7e84c83b72
ms.openlocfilehash: 145067c2ce50b41c4783f4de4052c0e7cb529fc7


---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium"></a>Управление режимом кэширования Azure CDN с помощью строк запросов (ценовая категория "Премиум")
> [!div class="op_single_selector"]
> * [Стандартный](cdn-query-string.md)
> * [Azure CDN уровня "Премиум" от Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Обзор
Функция кэширования строк запроса управляет способом кэширования файлов, содержащих строки запроса.

> [!IMPORTANT]
> Продукты CDN уровня "Стандартный" и "Премиум" предоставляют одинаковые возможности кэширования строк запроса, но имеют разные пользовательские интерфейсы.  В этом документе описывается пользовательский интерфейс для **Azure CDN уровня "Премиум" от Verizon**.  Дополнительные сведения о кэшировании строк запроса с помощью **Azure CDN уровня "Стандартный" от Akamai** и **Azure CDN уровня "Стандартный" от Verizon** см. в статье [Управление режимом кэширования запросов CDN с использованием строк запроса](cdn-query-string.md).
> 
> 

Доступны три режима:

* **стандартный кэш**: этот режим используется по умолчанию.  Граничный узел CDN передает строку запроса от запрашивающей стороны в источник по первому запросу и кэширует ресурс-контейнер.  Все последующие запросы к этому ресурсу, которые обслуживаются из граничного узла, будут пропускать строку запроса до истечения срока действия кэшированного ресурса.
* **без кэша**: в этом режиме запросы со строками запроса не кэшируются в граничном узле CDN.  Граничный узел получает ресурс непосредственно из источника и передает его запрашивающей стороне с каждым запросом.
* **уникальный кэш**: этот режим обрабатывает каждый запрос со строкой запроса как уникальный ресурс с собственным кэшем.  Например, ответ из источника для запроса к *foo.ashx?q=bar* будет сохранен в кэше на граничном узле и возвращен для последующих кэшей с этой же строкой запроса.  Запрос к *foo.ashx?q=somethingelse* будет кэшироваться как отдельный ресурс-контейнер с собственным сроком действия.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Изменение параметров кэширования строки запроса для профилей CDN уровня "Премиум"
1. В колонке профиля сети CDN нажмите кнопку **Управление** .
   
    ![Кнопка управления в колонке профиля CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Откроется портал управления CDN.
2. Наведите указатель мыши на вкладку **HTTP Large** (Большая платформа HTTP), а затем наведите указатель мыши на всплывающий элемент **Параметры кэша**.  Щелкните **Кэширование строк запроса**.
   
    Появятся параметры кэширования строк запроса.
   
    ![Параметры кэширования строк запроса CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. После выбора нажмите кнопку **Обновить** .

> [!IMPORTANT]
> Изменения параметров вступают в силу не сразу, так как распространение регистрационных сведений по сети CDN может занять некоторое время.  Для профилей <b>Azure CDN от Verizon</b> распространение обычно завершается в течение 90 минут, но в некоторых случаях может занимать больше времени.
> 
> 




<!--HONumber=Jan17_HO4-->


