---
title: "Установка драйвера серии N для Linux | Документация Майкрософт"
description: "Как установить драйверы NVIDIA GPU для виртуальных машин серии N под управлением Linux в Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Установка драйверов GPU для виртуальных машин серии N под управлением Linux

Чтобы воспользоваться преимуществами возможностей GPU виртуальных машин Azure серии N под управлением Linux, необходимо установить графические драйверы NVIDIA на каждую виртуальную машину. В этой статье приводятся действия по установке драйверов после развертывания виртуальных машин серии N. Сведения об установке драйверов также доступны для [виртуальных машин Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Характеристики виртуальных машин серии N, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин Linux, оптимизированных для GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## <a name="supported-distributions-and-drivers"></a>Поддерживаемые дистрибутивы и драйверы

> [!IMPORTANT]
> В настоящее время поддержка драйверов Linux GPU доступна только на виртуальных машинах Azure серии NC. 

Ниже перечислены дистрибутивы из Azure Marketplace, которые поддерживают использование графических драйверов NVIDIA на виртуальных машинах Linux серии N.

### <a name="nc-vms-tesla-k80-card"></a>Виртуальные машины серии NC (карта Tesla K80)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* Версия 7.3 на основе CentOS 

**Поддерживаемые драйверы**: NVIDIA CUDA 8.0, драйверы серии R375. [Действия по установке](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> Установка стороннего программного обеспечения на продуктах Red Hat может нарушать условия технической поддержки Red Hat. Ознакомьтесь со [статьей из базы знаний Red Hat](https://access.redhat.com/articles/1067).
>




## <a name="install-cuda-drivers-for-nc-vms"></a>Установка драйверов CUDA для виртуальных машин серии NC

Ниже приведены инструкции по установке драйверов NVIDIA из набора средств NVIDIA CUDA Toolkit 8.0 на виртуальные машины NC под управлением Linux. 

Разработчики на языках C и C++ могут дополнительно установить полный набор средств для создания приложений, использующих ускорение на GPU. Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Предоставленные в статье ссылки для скачивания драйверов CUDA актуальны на момент ее публикации. Последние версии драйверов можно получить на веб-сайте [NVIDIA](http://www.nvidia.com/).
>

Чтобы установить набор средств CUDA, установите SSH-подключение к каждой виртуальной машине. Убедитесь, что в системе есть графический процессор с архитектурой CUDA, выполнив следующую команду:

```bash
lspci | grep -i NVIDIA
```
Вы увидите данные, похожие на следующий пример (для адаптера NVIDIA Tesla K80):

![результаты выполнения команды lspci](./media/n-series-driver-setup/lspci.png)

Затем выполните команды, относящиеся к используемому дистрибутиву.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
Установка может занять несколько минут.

Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

```bash
sudo apt-get install cuda
```

Перезапустите виртуальную машину и приступите к проверке установки.

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 или Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> Из-за известной проблемы установка драйверов NVIDIA CUDA заканчивается сбоем на виртуальных машинах NC24r под управлением CentOS 7.3 или Red Hat Enterprise Linux 7.3.
>

Во-первых, установите обновления. 

```bash
sudo yum update

sudo reboot
```

Повторно подключитесь к виртуальной машине и продолжите установку с помощью следующих команд:

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

Установка может занять несколько минут. Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

```bash
sudo yum install cuda
```

Перезапустите виртуальную машину и приступите к проверке установки.


### <a name="verify-driver-installation"></a>Проверка установки драйверов


Чтобы запросить состояние устройства GPU, установите SSH-подключение к виртуальной машине и запустите служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

Отобразятся выходные данные, аналогичные приведенным ниже.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>Обновления драйверов CUDA

Рекомендуется периодически обновлять драйверы CUDA после развертывания.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

После завершения обновления перезапустите виртуальную машину.

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 или Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

После завершения обновления перезапустите виртуальную машину.



## <a name="troubleshooting"></a>Устранение неполадок

* Может возникать известная проблема, связанная с работой драйверов CUDA на виртуальных машинах Azure серии N под управлением ядра Linux версии 4.4.0-75 на Ubuntu 16.04 LTS. Чтобы драйвер продолжал работать после обновления ядра, выполните обновление как минимум до версии 4.4.0-77. 



## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графических процессорах NVIDIA в виртуальных машинах серии N см. в следующих разделах:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (для виртуальных машин Azure серии NC);
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (для виртуальных машин Azure серии NV).

* Чтобы записать образ виртуальной машины Linux с установленными драйверами NVIDIA, см. статью [Как подготовить к работе и записать образ виртуальной машины Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
