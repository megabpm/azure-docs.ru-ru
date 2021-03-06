---
title: "Восстановление данных с другого Сервера резервного копирования Azure | Документация Майкрософт"
description: "Восстановите данные, защищенные в хранилище архивации Azure, с любого Сервера резервного копирования Azure, зарегистрированного в этом хранилище."
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: adigan;giridham;trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 14cc190a7d1cde1181a6f26ef83095bc601f7fbb
ms.openlocfilehash: 36c4e1865c99dd1c55be798e1d310a02f2af0864


---
# <a name="recovering-data-from-another-azure-backup-server-in-the-backup-vault"></a>Восстановление данных с другого Сервера резервного копирования Azure в хранилище архивации
Теперь можно восстановить данные, защищенные в хранилище службы архивации Azure, с любого Сервера резервного копирования Azure, зарегистрированного в этом хранилище. Этот процесс полностью интегрирован в консоль управления ABS и похож на другие рабочие процессы восстановления.

> [!NOTE]
> Эта статья и следующие шаги применимы также к [System Center Data Protection Manager UR7] (https://support.microsoft.com/en-us/kb/3065246) с [последней версией агента службы архивации Azure](http://aka.ms/azurebackup_agent).
>
>

## <a name="recover-data-from-another-azure-backup-server"></a>Восстановление данных с другого Сервера резервного копирования Azure
Для восстановления данных с другого Сервера резервного копирования Azure сделайте следующее:

1. На вкладке **Восстановление** консоли управления ABS щелкните **Добавить внешний DPM** (в верхнем левом углу экрана).   
    ![Добавить внешний DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Скачайте новые **учетные данные хранилища** из хранилища, связанного с **Сервером резервного копирования Azure**, на котором восстанавливаются данные. Затем выберите Сервер резервного копирования Azure в списке серверов ABS, зарегистрированных в хранилище архивации, и укажите **парольную фразу для шифрования**, связанную с сервером, данные для которого восстанавливаются.

    ![Учетные данные для внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Данные на Серверах резервного копирования Azure можно восстановить только с серверов ABS, зарегистрированных в том же хранилище архивации.
   >
   >

    После успешного добавления внешнего Сервера резервного копирования Azure данные внешнего и локального серверов ABS можно просмотреть на вкладке **Восстановление**.
3. Просмотрите список доступных рабочих серверов, защищенных внешним Сервером резервного копирования Azure, и выберите необходимый источник данных.

    ![Обзор внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Выберите **месяц и год** из раскрывающегося списка **Точки восстановления**, затем выберите необходимую **дату восстановления** для даты создания точки восстановления и выберите **Время восстановления**.

    Снизу отображается список файлов и каталогов, который можно просматривать и восстанавливать до любой точки.

    ![Точки восстановления с внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Щелкните правой кнопкой мыши на соответствующем элементе и выберите **Восстановить**.

    ![Восстановление с внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Просмотрите содержимое раздела **Восстановить выделенное**. Проверьте дату и время восстанавливаемой резервной копии, а также источник, из которого была создана резервная копия. Если выбор был сделан неправильно, нажмите кнопку **Отмена** , чтобы вернуться обратно на вкладку и выбрать другую точку восстановления. Если выбор сделан правильно, щелкните **Далее**.

    ![Сводка восстановления с внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Выберите **Восстановление в другое расположение**. **Обзор** выберите расположение для восстановления.

    ![Альтернативное расположение для восстановления с внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Выберите один из вариантов: **Создать копию**, **Пропустить** или **Перезаписать**.

   * **Создать копию** в случае конфликта имен файлов будет создана копия файла.
   * **Пропустить** в случае конфликта имен файлов восстановление этого файла будет пропущено.
   * **Перезаписать** в случае конфликта имен файлов существующий файл в выбранном расположении будет перезаписан.

     Выберите соответствующее значение параметра **Восстановить параметры безопасности**. Можно применить параметры безопасности конечного компьютера, на который восстанавливаются данные, или параметры безопасности, которые использовались при создании точки восстановления.

     Укажите, необходимо ли отправить **Уведомление** после успешного завершения восстановления.

     ![Уведомление о восстановлении с внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. В окне **Сводка** перечислены все выбранные на данный момент параметры. После нажатия кнопки **Восстановить**данные будут восстановлены в соответствующее локальное расположение.

    ![Сводка параметров восстановления с внешнего сервера DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Ход восстановления можно отслеживать на вкладке **Мониторинг** Сервера резервного копирования Azure.
   >
   >

    ![Мониторинг восстановления](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Для удаления внешнего сервера DPM на вкладке **Восстановление** сервера DPM можно щелкнуть **Очистить внешний DPM**.

    ![Удалить внешний сервер DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Сообщения об ошибках при устранении неполадок
| Нет. | Сообщение об ошибке | Действия по устранению неполадок |
|:---:|:--- |:--- |
| 1. |Этот сервер не зарегистрирован в хранилище, заданном с помощью учетных данных хранилища. |**Причина:** эта ошибка возникает, когда выбранный файл учетных данных хранилища не соответствует хранилищу архивации, связанному с Сервером резервного копирования Azure, на котором выполняется попытка восстановления. <br> **Решение:** скачайте файл учетных данных хранилища из хранилища архивации, в котором зарегистрирован Сервер резервного копирования Azure. |
| 2) |Восстанавливаемые данные недоступны или выбранный сервер не является сервером DPM. |**Причина:** нет других Серверов резервного копирования Azure, зарегистрированных в хранилище архивации, либо на серверах еще не загрузились метаданные, либо выбранный сервер не является Сервером резервного копирования Azure (также известным как Windows Server или Windows Client). <br> **Решение:** если в хранилище архивации есть другие зарегистрированные Серверы резервного копирования Azure, убедитесь, что последняя версия агента хранилища Azure установлена. <br>Если в хранилище архивации есть другие зарегистрированные Серверы резервного копирования Azure, подождите один день после установки. Затем можно приступить к процессу восстановления. Задания, запланированные на ночь, отправят метаданные для всех защищенных хранилищ в облако. Данные будут доступны для восстановления. |
| 3. |В этом хранилище не зарегистрировано другого сервера DPM. |**Причина:** нет других Серверов резервного копирования Azure, зарегистрированных в хранилище, на котором выполняется попытка восстановления.<br>**Решение:** если в хранилище архивации есть другие зарегистрированные Серверы резервного копирования Azure, убедитесь, что последняя версия агента службы архивации Azure установлена.<br>Если в хранилище архивации есть другие зарегистрированные Серверы резервного копирования Azure, подождите один день после установки. Затем можно приступить к процессу восстановления. Задания, запланированные на ночь, отправят метаданные для всех защищенных хранилищ в облако. Данные будут доступны для восстановления. |
| 4. |Указанная парольная фраза не соответствует парольной фразе, связанной со следующим сервером: **<server name>** |**Причина:** указанная парольная фраза не соответствует парольной фразе для шифрования, использованной при шифровании восстанавливаемых данных Сервера резервного копирования Azure. Агенту не удалось расшифровать данные. Поэтому восстановление завершается неудачно.<br>**Решение:** укажите именно ту парольную фразу, которая связана с Сервером резервного копирования Azure, данные для которого восстанавливаются. |

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
1. **Почему не удается добавить внешний сервер DPM с другого сервера DPM после установки UR7 и последней версии агента службы архивации Azure? (Применяется в случае использования SC DPM 2012 R2)**

    Ответ. Для имеющихся серверов DPM с источниками данных, защищенными в облаке (с помощью накопительного пакета обновления более ранней версии, чем 7), необходимо подождать по крайней мере один день после установки UR7 и последней версии агента службы архивации Azure. После этого можно *добавить внешний сервер DPM*. Это необходимо для загрузки метаданных в группы защиты DPM в Azure. Загрузка выполняется в задании, запланированном на ночь.
2. **Какова минимальная требуемая версия агента хранилища Azure?**

    Ответ. Минимальная версия агента хранилища Azure, в котором есть эта возможность, — 2.0.8719.0.  Чтобы проверить версию агента службы архивации Azure, выберите "Панель управления **>** Все элементы панели управления **>** Программы и компоненты **>** Агент служб восстановления Microsoft Azure". Если версия агента менее 2.0.8719.0, загрузите [последнюю версию агента службы архивации Azure](https://go.microsoft.com/fwLink/?LinkID=288905) и установите ее.

    ![Удалить внешний сервер DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Дальнейшие действия
•    [Служба архивации Azure: часто задаваемые вопросы](backup-azure-backup-faq.md)



<!--HONumber=Jan17_HO4-->


