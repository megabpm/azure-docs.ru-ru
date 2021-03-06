---
title: "Требования к качеству обслуживания для ExpressRoute | Документация Майкрософт"
description: "На этой странице подробно описаны требования по настройке качества обслуживания для каналов ExpressRoute и управлению им."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: db1c1447-0283-4a09-907b-ae481adc40c7
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e74127e3140ec9973753fb5f29151c406326c36


---
# <a name="expressroute-qos-requirements"></a>Требования к качеству обслуживания для ExpressRoute
В Skype для бизнеса имеются различные рабочие нагрузки, требующие дифференцированного подхода к качеству обслуживания. Если вы планируете использовать голосовые службы с помощью ExpressRoute, то необходимо соблюдать указанные ниже требования.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Требования к качеству обслуживания применяются только к пирингу Майкрософт. Значения DSCP в сетевом трафике, полученном через общедоступный или частный пиринг Azure, будут сброшены до "0". 
> 
> 

В таблице ниже перечислены пометки DSCP, используемые в Skype для бизнеса. Дополнительные сведения см. в статье [Управление качеством обслуживания для Skype для бизнеса](https://technet.microsoft.com/library/gg405409.aspx).

| **Класс трафика** | **Обработка (пометки DSCP)** | **Рабочие нагрузки Skype для бизнеса** |
| --- | --- | --- |
| **Голосовая связь** |EF (46) |Голосовая связь Skype и Lync |
| **Интерактивный** |AF41 (34) |Видео |
| AF21 (18) |Совместный доступ к приложениям | |
| **По умолчанию** |AF11 (10) |Передача файлов |
| CS0 (0) |Другие варианты | |

* Вам необходимо классифицировать рабочие нагрузки и пометить правильные значения DSCP. Следуйте указанным [здесь](https://technet.microsoft.com/library/gg405409.aspx) инструкциям по настройке пометок DSCP в сети.
* Вам необходимо настроить и поддерживать несколько очередей качества обслуживания в сети. Голосовая связь должна быть изолированным классом и использовать обработку EF согласно документу RFC 3246. 
* Вы можете решить, какие механизм очередей, политику обнаружения перегрузки и пропускную способность использовать для каждого класса трафика. Тем не менее необходимо сохранить пометки DSCP для рабочих нагрузок Skype для бизнеса. Если вы используете пометки DSCP, которых нет в списке выше, например AF31 (26), то перед отправкой пакета в корпорацию Майкрософт необходимо перезаписать это значение DSCP и сделать его равным 0. Корпорация Майкрософт отправляет только те пакеты, которые помечены значениями DSCP, указанными в таблице выше. 

## <a name="next-steps"></a>Дальнейшие действия
* См. сведения о требованиях для [маршрутизации](expressroute-routing.md) и [преобразования сетевых адресов (NAT)](expressroute-nat.md).
* Чтобы настроить подключение ExpressRoute, см. следующие статьи:
  
  * [Создание канала ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Настройка маршрутизации](expressroute-howto-routing-classic.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Nov16_HO2-->


