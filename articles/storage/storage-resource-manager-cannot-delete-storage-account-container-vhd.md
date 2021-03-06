---
title: "Устранение неполадок при удалении учетных записей хранения Azure, контейнеров или виртуальных жестких дисков в модели развертывания с помощью Resource Manager | Документация Майкрософт"
description: "Устранение ошибок при удалении учетных записей хранения Azure, контейнеров или виртуальных жестких дисков в модели развертывания с помощью Resource Manager"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a
ms.contentlocale: ru-ru
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Устранение ошибок при удалении учетных записей хранения Azure, контейнеров или виртуальных жестких дисков в модели развертывания с помощью Resource Manager
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

При попытке удалить учетную запись хранения Azure, контейнер или виртуальный жесткий диск (VHD) на [портале Azure](https://portal.azure.com) могут возникать ошибки. В этой статье содержатся рекомендации по устранению ошибок в модели развертывания с помощью Azure Resource Manager.

Если эта статья не помогла устранить проблему с Azure, посетите форумы по Azure на сайтах [MSDN и Stack Overflow](https://azure.microsoft.com/support/forums/). Проблему можно разместить на этих форумах или отправить по адресу @AzureSupport в Twitter. Кроме того, можно отправить запрос в службу поддержки Azure, выбрав **Получить поддержку** на сайте [службы поддержки Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Проблемы
### <a name="scenario-1"></a>Сценарий 1
При попытке удалить виртуальный жесткий диск в учетной записи хранения в модели развертывания с помощью Resource Manager пользователь получил следующее сообщение об ошибке:

**Не удалось удалить большой двоичный объект vhds/BlobName.vhd. Ошибка: There is currently a lease on the blob and no lease ID was specified in the request (В настоящий момент большой двоичный объект находится в аренде, и в запросе не указан идентификатор аренды).**

Эта проблема может возникнуть, если виртуальный жесткий диск, который вы пытаетесь удалить, по-прежнему находится в аренде на виртуальной машине.

### <a name="scenario-2"></a>Сценарий 2
При попытке удалить контейнер в учетной записи хранения в модели развертывания с помощью Resource Manager пользователь получил следующее сообщение об ошибке:

**Не удалось удалить контейнер хранилища vhds. Ошибка: There is currently a lease on the container and no lease ID was specified in the request (В настоящий момент контейнер находится в аренде, и в запросе не указан идентификатор аренды).**

Эта проблема может возникнуть, если контейнер содержит виртуальный жесткий диск, заблокированный в состоянии аренды.

### <a name="scenario-3"></a>Сценарий 3
При попытке удалить учетную запись хранения в модели развертывания с помощью Resource Manager пользователь получил следующее сообщение об ошибке:

**Не удалось удалить учетную запись хранения "имя_учетной_записи_хранения". Ошибка: "Не удается удалить учетную запись хранения, так как ее артефакты используются".**

Эта проблема может возникнуть, если учетная запись хранения содержит виртуальный жесткий диск, который находится в состоянии аренды.

## <a name="solution"></a>Решение
Чтобы устранить эти проблемы, необходимо определить виртуальный жесткий диск, который вызывает ошибку, и связанную виртуальную машину. Затем отсоедините виртуальный жесткий диск от виртуальной машины (для дисков данных) или удалите виртуальную машину, на которой используется виртуальный жесткий диск (для дисков ОС). Это позволит вывести виртуальный жесткий диск из состояния аренды и удалить его.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Шаг 1. Определение виртуального жесткого диска, вызвавшего ошибку, и связанной виртуальной машины
1. Войдите на [портал Azure](https://portal.azure.com).
2. В **главном меню** выберите **Все ресурсы**. Перейдите к учетной записи хранения, которую нужно удалить, и выберите **BLOB-объекты** > **vhds**.

    ![Снимок экрана: учетная запись хранения с выделенным контейнером vhds на портале](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. Проверьте свойства каждого виртуального жесткого диска в контейнере. Найдите виртуальный жесткий диск, который находится в состоянии **В аренде**. Затем определите виртуальную машину, использующую этот виртуальный жесткий диск. Как правило, это можно сделать по имени виртуального жесткого диска:

   * Диски ОС используют следующее соглашение об именовании: "Имя_ВМYYYYMMDDHHMMSS.vhd";
   * Диски данных используют следующее соглашение об именовании: "Имя_ВМ-YYYYMMDD-HHMMSS.vhd".

     ![Снимок экрана: сведения о контейнере на портале, в том числе имя виртуальной машины, состояние аренды "Заблокировано" и "В аренде" (выделено)](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Шаг 2. Удаление аренды виртуального жесткого диска
Чтобы удалить виртуальную машину, использующую виртуальный жесткий диск (для дисков ОС), сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В **главном** меню выберите **Виртуальные машины**.
3. Выберите виртуальную машину, арендующую виртуальный жесткий диск.
4. Убедитесь, что виртуальная машина больше не используется и в ней нет необходимости.
5. В верхней части колонки **сведений о виртуальной машине** выберите **Удалить**, а затем нажмите кнопку **Да**, чтобы подтвердить.
6. После этого виртуальная машина будет удалена, но виртуальный жесткий диск останется. Однако он выйдет из состояния аренды. Освобождение от аренды может занять несколько минут. Чтобы убедиться, что аренда удалена, выберите **Все ресурсы** > **Имя учетной записи хранения** > **BLOB-объекты** > **vhds**. В области **Свойства BLOB-объекта** для параметра **Состояние аренды** должно быть задано значение **Разблокировано**.

Чтобы отключить виртуальный жесткий диск от виртуальной машины, использующей его (для дисков данных), сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В **главном** меню выберите **Виртуальные машины**.
3. Выберите виртуальную машину, арендующую виртуальный жесткий диск.
4. В колонке **сведений о виртуальной машине** выберите **Диски**.
5. Выберите диск данных, арендующий виртуальный жесткий диск. Виртуальный жесткий диск, присоединенный к диску данных, можно определить, просмотрев его URL-адрес.
6. Убедитесь, что диск данных больше не используется.
7. В колонке **Сведения о диске** щелкните **Отключить**.
8. После этого диск будет отсоединен от виртуальной машины, а виртуальный жесткий диск освобожден от аренды. Освобождение от аренды может занять несколько минут. Чтобы убедиться, что аренда удалена, выберите **Все ресурсы** > **Имя учетной записи хранения** > **BLOB-объекты** > **vhds**. В области **Свойства BLOB-объекта** для параметра **Состояние аренды** должно быть задано значение **Разблокировано**.

## <a name="what-is-a-lease"></a>Общие сведения об аренде
Аренда — это блокировка, используемая для контроля доступа к большому двоичному объекту (например, к виртуальному жесткому диску). Если большой двоичный объект находится в состоянии аренды, к нему могут получить доступ только владельцы аренды. Аренда предоставляет следующие возможности:

* предотвращает повреждение данных при одновременной записи несколькими владельцами данных в ту же самую часть большого двоичного объекта;
* предотвращает удаление большого двоичного объекта в случае его активного использования (например, виртуальной машиной);
* предотвращает удаление учетной записи хранения в случае ее активного использования (например, виртуальной машиной).

## <a name="next-steps"></a>Дальнейшие действия
* [Удаление учетной записи хранения](storage-create-storage-account.md#delete-a-storage-account)
* [Приостановка заблокированной аренды хранилища BLOB-объектов в Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

