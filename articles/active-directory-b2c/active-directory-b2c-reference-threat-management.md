---
title: "Предотвращение угроз в Azure B2C | Документация Майкрософт"
description: "Описываются методики обнаружения и противодействия DOS-атакам и атакам для взлома пароля в Azure B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C: предотвращение угроз
Предотвращение угроз включает в себя защиту от атак, нацеленных на систему и сети. Атака типа "отказ в обслуживании" (DOS) может негативно влиять на доступность ресурса, который становится недоступным для предполагаемых пользователей. Атаки для взлома пароля приводят к несанкционированному доступу к ресурсам. Служба Microsoft Azure Active Directory B2C оснащена несколькими встроенными средствами защиты данных от таких угроз. 

## <a name="denial-of-service-attack"></a>Атака типа "отказ в обслуживании"

Для защиты базовых ресурсов от таких атак Azure AD B2C использует методики обнаружения и уменьшения негативных последствий, такие как файлы cookie SYN, ограничение скорости и количества подключений.  

## <a name="password-attacks"></a>Атаки для взлома пароля

Служба Azure AD B2C также имеет средства противодействия атакам для взлома пароля.  Эта методика действует как при атаках методом подбора пароля, так и при словарных атаках на пароль.  Пароли, задаваемые пользователями, должны быть достаточно сложными.  Используя различные сигналы, Azure AD B2C анализирует целостность запросов, чтобы интеллектуально отличить предполагаемых пользователей от злоумышленников и ботнетов. Служба B2C применяет сложные стратегии для блокировки учетных записей, анализируя вводимые пароли и выявляя потенциальные атаки.

[Дополнительные сведения о предотвращении угроз в продуктах корпорации Майкрософт](https://www.microsoft.com/trustcenter/security/threatmanagement)
