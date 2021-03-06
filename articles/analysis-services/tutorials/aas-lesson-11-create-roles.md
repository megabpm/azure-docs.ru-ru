---
title: "Учебник по службам Azure Analysis Services: занятие 11 &quot;Создание ролей&quot; | Документы Майкрософт"
description: "Описание создания ролей в учебном проекте служб Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: eea9b247b42db81f30b7169f71ddf0d5068f6a5e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-11-create-roles"></a>Занятие 11. Создание ролей

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

На этом занятии мы создадим роли. Роли обеспечивают безопасность данных и объектов базы данных модели, разрешая доступ только тем пользователям, которые являются членами роли. Каждая роль определяется с помощью одного разрешения: "Нет", "Чтение", "Чтение и обработка", "Обработка" или "Администратор". Роли можно определить во время создания модели с помощью диспетчера ролей. После развертывания модели ролями можно управлять с помощью SQL Server Management Studio (SSMS). Дополнительные сведения см.в статье [Роли](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> Создание ролей не является обязательным для прохождения этого учебника. По умолчанию учетная запись, с которой вы выполнили вход, будет иметь права администратора для модели. Тем не менее, чтобы разрешить другим пользователям в организации просматривать модель с помощью клиента отчетов, необходимо создать хотя бы одну роль с разрешениями на чтение и добавить этих пользователей в качестве членов роли.  
  
Мы создадим три роли.  
  
-   **Менеджер по продажам** — эта роль может включать пользователей вашей организации, которым вы ходите предоставить права на чтение всех объектов и данных модели.  
  
-   **Аналитик по сбыту в США** — эта роль может включать пользователей вашей организации, для которых требуется только возможность просмотра данных, связанных с продажами в США. Для этой роли будет использоваться формула DAX для определения *фильтра строк*, который позволит членам роли просматривать данные только для США.  
  
-   **Администратор** — эта роль может включать пользователей, для которых требуются права администратора, то есть неограниченный доступ и разрешения для выполнения административных задач в базе данных модели.  
  
Поскольку учетные записи пользователей и групп Windows в вашей организации уникальны, вы можете добавить в роль учетные записи из вашей организации. Однако в рамках этого учебника участников роли можно не указывать. В любом случае, вы сможете проверить влияние каждой из ролей позднее в занятии 12 "Анализ в Excel".  
  
Предполагаемое время выполнения этого занятия: **15 минут**  
  
## <a name="prerequisites"></a>Предварительные требования  
Этот раздел входит в учебник по табличному моделированию, который следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, необходимо завершить предыдущее занятие: [Занятие 10. Создание разделов](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Создание ролей  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Создание роли пользователя "Менеджер по продажам"  
  
1.  В обозревателе табличных моделей щелкните правой кнопкой мыши **Роли** > **Роли**.  
  
2.  В диспетчере ролей нажмите кнопку **Создать**.  
  
3.  Щелкните новую роль, а затем в столбце **Имя** присвойте роли имя **Менеджер по продажам**.  
  
4.  В столбце **Разрешения** щелкните раскрывающийся список и выберите разрешение **Чтение**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Дополнительно: откройте вкладку **Члены**, а затем щелкните **Добавить**. В диалоговом окне **Выбор пользователей или групп** укажите пользователей или группы Windows из вашей организации, которым вы хотите назначить эту роль.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Создание роли пользователя "Аналитик по сбыту в США"  
  
1.  В диспетчере ролей нажмите кнопку **Создать**.    
  
2.  Переименуйте роль в **Аналитик по сбыту в США**.  
  
3.  Предоставьте этой роли разрешение **Чтение**.  
  
4.  Перейдите на вкладку "Фильтры строк", а затем только для таблицы **DimGeography** в столбце "Фильтр DAX" введите следующую формулу:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Формула фильтра строк должна разрешаться в логическое значение (TRUE/FALSE). С помощью этой формулы вы указываете, что пользователю будут видны только строки со значением кода страны или региона US (США).  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Дополнительно: откройте вкладку **Члены**, а затем щелкните **Добавить**. В диалоговом окне **Выбор пользователей или групп** укажите пользователей или группы Windows из вашей организации, которым вы хотите назначить эту роль.  
  
#### <a name="to-create-an-administrator-user-role"></a>Создание роли пользователя "Администратор"  
  
1.  Нажмите кнопку **Создать**.  
  
2.  Переименуйте роль в **Администратор**.  
  
3.  Предоставьте этой роли разрешение **Администратор**.  
  
4.  Дополнительно: откройте вкладку **Члены**, а затем щелкните **Добавить**. В диалоговом окне **Выбор пользователей или групп** укажите пользователей или группы Windows из вашей организации, которым вы хотите назначить эту роль. 
  
  
## <a name="whats-next"></a>Что дальше?
[Занятие 12. Анализ в Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  

