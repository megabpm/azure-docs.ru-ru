---
title: "Подключение управляемого диска данных к виртуальной машине Windows в Azure | Документация Майкрософт"
description: "Подключение нового управляемого диска данных к виртуальной машине Windows на портале Azure с помощью модели развертывания на основе диспетчера ресурсов."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9c29390756ac2cd925ed7d2989393e63ed0a1239
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Как подключить управляемый диск данных к виртуальной машине Windows на портале Azure

В этой статье демонстрируется подключение нового управляемого диска данных к виртуальной машине Windows на портале Azure. Перед этим ознакомьтесь со следующими советами:

* Размер виртуальной машины определяет, сколько дисков данных к ней можно подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](sizes.md).
* Если вы подключаете новый диск, его не надо предварительно создавать. Azure создаст его при подключении.

Вы также можете [присоединить диск данных с помощью Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Добавление диска данных
1. В меню слева щелкните **Виртуальные машины**.
2. Затем выберите виртуальную машину из списка.
3. В колонке виртуальной машины щелкните **Диски**.
   4. В колонке **Диски** щелкните **+ Add data disk** (+ Добавить диск данных).
5. В раскрывающемся списке для нового диска выберите **Create empty** (Создать пустой).
6. В колонке **Создание управляемого диска** введите имя диска и при необходимости настройте другие параметры. Закончив, щелкните **Создать**.
7. В колонке **Диски** щелкните "Сохранить", чтобы сохранить новую конфигурацию диска для виртуальной машины.
6. После того как Azure создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**.


## <a name="initialize-a-new-data-disk"></a>Инициализация нового диска данных

1. Подключитесь к виртуальной машине.
1. Щелкните меню "Пуск" на виртуальной машине, введите **diskmgmt.msc** и нажмите клавишу **ВВОД**. Запустится оснастка "Управление дисками".
2. Оснастка "Управление дисками" определит новый неинициализированный диск, и откроется окно "Инициализация диска".
3. Убедитесь, что выбран новый диск, и нажмите кнопку **ОК**, чтобы его инициализировать.
4. Теперь новый диск отображается как **нераспределенный**. Щелкните правой кнопкой мыши в любой части диска и выберите **Создать простой том**. Будет запущен **мастер создания простых томов**.
5. Выполните инструкции мастера, оставляя все значения по умолчанию. По завершении щелкните **Готово**.
6. Закройте оснастку "Управление дисками".
7. Появится всплывающее окно, в котором необходимо форматировать диск перед его использованием. Щелкните **Форматировать диск**.
8. В диалоговом окне **Format new disk** (Форматирование нового диска) проверьте параметры и щелкните **Запустить**.
9. Вы получите предупреждение о том, что при форматировании дисков будут стерты все данные. Нажмите кнопку **ОК**.
10. После форматирования нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дальнейшие действия
Если вашему приложению нужно использовать диск D для хранения данных, вы можете [изменить букву временного диска Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

