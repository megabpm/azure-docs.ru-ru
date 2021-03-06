---
title: "Получение оповещений журнала действий для уведомлений службы | Документация Майкрософт"
description: "Получать уведомления по SMS, электронной почте или Webhook при появлении службы Azure."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 57e136c1027cfa7c789803409cef354f3d52d222
ms.lasthandoff: 03/31/2017


---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Создание оповещений журнала действий для уведомлений службы
## <a name="overview"></a>Обзор
В этой статье показано, как настроить оповещения журнала действий для уведомлений о работоспособности службы на портале Azure.  

Оповещения можно получать на основе уведомлений о работоспособности службы для своей подписки Azure.  
Оповещения можно настроить на основе следующих данных.
- Класс уведомления о работоспособности службы (инцидент, обслуживание, сведения и т. д.)
- Состояние уведомления (активное или завершенное).
- Уровень уведомления (информационное, предупреждение, ошибка).

Также можно настроить, кому должны отправляться оповещения.
- Выбор существующей группы действий
- Создание новой группы действий (которая позднее может использоваться для будущих оповещений)

Дополнительные сведения о группах действий см. [здесь](monitoring-action-groups.md).

Настроить и получать сведения об оповещениях для уведомлений о работоспособности службы можно с помощью
- [Портал Azure](monitoring-activity-log-alerts-on-service-notifications.md)
- [Шаблоны Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью портала Azure
1.    На [портале](https://portal.azure.com) перейдите на вкладку **Монитор** службы.

    ![Монитор](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.    Щелкните **Мониторинг**, чтобы открыть колонку "Мониторинг". Сначала открывается раздел **Журнал действий** .

3.    Теперь щелкните раздел **Оповещения**.

    ![Оповещения](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.    Выберите команду **Добавить оповещение журнала действий** и заполните поля.

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.    Укажите **Имя** оповещения журнала действий и выберите **Описание**. Они будут отображаться в уведомлениях, отправляемых при срабатывании данного оповещения.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.    Поле **Подписка** должно автоматически заполняться сведениями для текущей подписки.

7.    Выберите **группу ресурсов** для этого оповещения.

8.    В разделе **Категория события** выберите параметр "Работоспособность службы". Выберите, о каких параметрах **тип, состояние** и **уровень** уведомлений о работоспособности службы нужно получать уведомления.

9.    Создайте **Новую** группу действий, указав ее **имя** и **короткое имя**; короткое имя будет указано в уведомлениях, отправляемых при срабатывании данного оповещения.

10.    Укажите список получателей, указав для каждого получателя

    А. **Имя:** имя, псевдоним или идентификатор получателя.

    b. **Тип действия:** выберите способ уведомления получателя — SMS, сообщение электронной почты или Webhook.

    c. **Подробные сведения:** в зависимости от выбранного типа действия, укажите номер телефона, адрес электронной почты или указатель URI Webhook.

11.    Завершив настройку, нажмите кнопку **ОК** , чтобы создать оповещение.

Через несколько минут оповещение включится и будет активироваться, как было описано выше.

Дополнительные сведения о схеме Webhook для оповещений журнала активности см. [здесь](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Группу действий, определенную на этих этапах, можно будет многократно использовать как существующую группу действий для всех будущих определений оповещений.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-with-the-azure-portal"></a>Создание оповещения для уведомления о работоспособности службы для существующей группы действий с помощью портала Azure
1.    На [портале](https://portal.azure.com) перейдите на вкладку **Монитор** службы.

    ![Монитор](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.    Щелкните **Мониторинг**, чтобы открыть колонку "Мониторинг". Сначала открывается раздел **Журнал действий** .

3.    Теперь щелкните раздел **Оповещения**.

    ![Оповещения](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.    Выберите команду **Добавить оповещение журнала действий** и заполните поля.

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.    Укажите **Имя** оповещения журнала действий и выберите **Описание**. Они будут отображаться в уведомлениях, отправляемых при срабатывании данного оповещения.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.    Поле **Подписка** должно автоматически заполняться сведениями для текущей подписки.

7.    Выберите **группу ресурсов** для этого оповещения.

8.    В разделе **Категория события** выберите параметр "Работоспособность службы". Выберите, о каких параметрах **тип, состояние** и **уровень** уведомлений о работоспособности службы нужно получать уведомления.

9.    Выберите для параметра **Notify Via** (Уведомлять через) значение **Existing action group** (Существующая группа действий). Выберите соответствующую группу действий.

10.    Завершив настройку, нажмите кнопку **ОК** , чтобы создать оповещение.

Через несколько минут оповещение включится и будет активироваться, как было описано выше.

## <a name="managing-your-alerts"></a>Управление оповещениями

После создания оповещения оно будет отображаться в разделе "Оповещения" службы мониторинга. Выберите оповещение, которым нужно управлять, чтобы получить возможность:
* **изменить** его;
* **удалить** его;
* **отключить** или **включить** его, если нужно временно остановить или возобновить получение уведомлений для этого оповещения.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об [уведомлениях о работоспособности службы](monitoring-service-notifications.md).  
Просмотрите [схему webhook для оповещения журнала действий](monitoring-activity-log-alerts-webhook.md) Ознакомьтесь с [обзором оповещений журнала действий](monitoring-overview-alerts.md) и узнайте, как получать оповещения  
Узнайте больше о [группах действий](monitoring-action-groups.md).

