---
title: "Управление базовыми политиками лаборатории в Azure DevTest Labs | Документация Майкрософт"
description: "Узнайте, как настроить некоторые базовые политики (параметры) для лаборатории в DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e5b622be66c3e009c67a00dd380efd87b013c920
ms.lasthandoff: 03/17/2017


---

# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Управление базовыми политиками лаборатории в Azure DevTest Labs

Azure DevTest Labs позволяет контролировать расходы и свести к минимуму излишние траты в лабораториях с помощью управления политиками (параметрами) для каждой лаборатории. В этой статье показано, как начать работу с политиками, настроив две наиболее важные политики — ограничение числа виртуальных машин, которые может создать или запросить один пользователь, и настройка автоматического завершения работы. Сведения о настройке каждой политики лаборатории см. в статье [Управление всеми политиками лаборатории в Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Оценка политик лаборатории в Azure DevTest Labs
Ниже приведены инструкции по настройке политик для лаборатории в Azure DevTest Labs.

Чтобы просмотреть (и изменить) политики для лаборатории, выполните следующее.

1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Другие службы**, а затем выберите в списке **DevTest Labs**.

1. Из списка лабораторий выберите нужную лабораторию.   

1. Выберите **Configuration and policies** (Конфигурация и политики).

    ![Колонка "Параметры политики"](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Колонка **Configuration and policies** (Конфигурация и политики) содержит меню параметров, которые можно задать. В этой статье рассматриваются только параметры **Virtual machines per user** (Количество виртуальных машин на пользователя) и **Автозавершение работы**. Сведения о настройке остальных параметров см. в статье [Управление всеми политиками лаборатории в Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Настройка количества виртуальных машин на пользователя
Политика для **Virtual machines per user** (Количество виртуальных машин на пользователя) позволяет указать максимальное число виртуальных машин, которые может создать отдельный пользователь. Если пользователь пытается создать или запросить виртуальную машину в случае достижения ограничения для пользователя, то выводится сообщение об ошибке, информирующее о невозможности создания или запроса виртуальной машины. 

1. В меню **Configuration and policies** (Конфигурация и политики) лаборатории выберите **Virtual machines per user** (Количество виртуальных машин на пользователя).
   
    ![Virtual machines per user](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Выберите **Да**, чтобы ограничить количество виртуальных машин на пользователя. Если вы не хотите ограничивать это количество, то выберите **Нет**. Если вы выбрали **Да**, то введите максимальное количество виртуальных машин, которые может создать или запросить пользователь. 

1. Выберите **Да**, чтобы ограничить количество виртуальных машин, которые могут использовать твердотельный накопитель (SSD). Если вы не хотите ограничивать это количество, то выберите **Нет**. Если вы выбрали **Да**, то введите максимальное количество виртуальных машин, которые можно создать с помощью SSD. 

1. Щелкните **Сохранить**.

## <a name="set-auto-shutdown"></a>Настройка автоматического завершения работы
Политика автоматического завершения работы помогает сократить издержки, позволяя указывать время выключения виртуальных машин в этой лаборатории.

1. В колонке **Configuration and Policies** (Конфигурация и политики) лаборатории выберите **Автозавершение работы**.
   
    ![Auto-shutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Щелкните **On** (Вкл.), чтобы включить эту политику, или **Off** (Выкл.), чтобы отключить ее.

1. Если эта политика включена, то укажите время (и часовой пояс) завершения работы всех виртуальных машин в текущей лаборатории.

1. Укажите значение **Да** или **Нет** для параметра отправки уведомления за 15 минут до времени автоматического завершения работы. Если выбрано значение **Да**, то введите конечную точку URL-адреса webhook для получения уведомления. Дополнительные сведения об объектах webhook см. в статье [Создание функции Azure объекта webhook или API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Щелкните **Сохранить**.

    По умолчанию после включения эта политика применяется ко всем виртуальным машинам в текущей лаборатории. Чтобы удалить этот параметр из конкретной виртуальной машины, откройте колонку виртуальной машины и измените значение параметра **Auto-shutdown** (Автоматическое завершение работы). 

## <a name="next-steps"></a>Дальнейшие действия

- [Управление всеми политиками лаборатории в Azure DevTest Labs](devtest-lab-set-lab-policy.md): сведения об изменении других политик лаборатории. 

