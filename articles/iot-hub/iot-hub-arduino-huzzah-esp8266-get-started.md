---
title: "Подключение Feather HUZZAH ESP8266 к Центру Интернета вещей Azure в облаке | Документация Майкрософт"
description: "В этой статье объясняется, как подключить устройство Arduino под названием Adafruit Feather HUZZAH ESP8266 к Центру Интернета вещей Azure — облачной службе Майкрософт, которая помогает управлять ресурсами Интернета вещей."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 3650f628747f8a9e743711f5c7a175d2a2523565
ms.lasthandoff: 04/12/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Подключение Adafruit Feather HUZZAH ESP8266 к Центру Интернета вещей Azure в облаке

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Подключение между DHT22, Feather HUZZAH ESP8266 и Центром Интернета вещей](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>В рамках этого руководства мы:


В рамках этого руководства мы подключим плату Adafruit Feather HUZZAH ESP8266 к созданному Центру Интернета вещей Azure. Затем мы запустим пример приложения на ESP8266, чтобы собрать данные о температуре и влажности, зафиксированные датчиком DHT22. После этого отправим данные с датчика в Центр Интернета вещей.

> [!NOTE]
> При использовании других плат ESP8266 вы по-прежнему можете выполнить эти действия, чтобы подключиться к Центру Интернета вещей. В зависимости от используемой платы ESP8266 вам может потребоваться изменить параметр `LED_PIN`. Например, если вы используете ESP8266 от AI-Thinker, вы можете изменить его значение с `0` на `2`. Нет начального набора? Получите его на [веб-сайте Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей и зарегистрировать устройство для Feather HUZZAH ESP8266.
* Как подключить Feather HUZZAH ESP8266 к датчику и компьютеру.
* Как собирать данные датчиков, запустив пример приложения на Feather HUZZAH ESP8266.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

![Элементы, необходимые в рамках руководства:](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Чтобы выполнить эту операцию, вам понадобятся следующие элементы из начального набора Feather HUZZAH ESP8266:

* плата Feather HUZZAH ESP8266;
* кабель micro-USB (тип A).

Кроме того, вам понадобятся следующие элементы среды разработки:

* ПК или компьютер Mac под управлением Windows или Ubuntu;
* беспроводная сеть, к которой подключается Feather HUZZAH ESP8266;
* подключение к Интернету для скачивания средства настройки;
* [Arduino IDE](https://www.arduino.cc/en/main/software) версии 1.6.8 или более новой. Более ранние версии несовместимы с библиотекой Центра Интернета вещей Azure.





Ниже приведены необязательные элементы, используемые в случае отсутствия датчика. Вы также можете использовать симулированные данные датчика.

* Датчик температуры и влажности Adafruit DHT22.
* Монтажная плата.
* Многомодовый оптоволоконный кабель с разъемами на обоих концах.

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>Создание Центра Интернета вещей и регистрация устройства для Feather HUZZAH ESP8266

### <a name="to-create-your-iot-hub-in-the-azure-portal-follow-these-steps"></a>Чтобы создать Центр Интернета вещей на портале Azure, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Щелкните **Создать** > **Интернет вещей** > **Центр Интернета вещей**.

   ![Создание Центра Интернета вещей](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. В области **Центр Интернета вещей** введите необходимые сведения для создания Центра Интернета вещей.

   ![Основные сведения для создания Центра Интернета вещей](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **Имя**. Имя Центра Интернета вещей. Если введенное имя является допустимым, появится зеленая галочка.
   * **Pricing and scale tier** (Ценовая категория и категория масштабирования). Выберите бесплатную категорию F1 для этого демонстрационного руководства. Дополнительные сведения о ценовой категории и категории масштабирования см. на [этой странице](https://azure.microsoft.com/pricing/details/iot-hub/).
   * **Группа ресурсов.** Создайте группу ресурсов для размещения Центра Интернета вещей или используйте существующую. Дополнительные сведения см. в статье [Управление ресурсами Azure через портал](../azure-resource-manager/resource-group-portal.md).
   * **Расположение**. Выберите расположение, ближайшее к созданному Центру Интернета вещей.
   * **Закрепить на панели мониторинга.** Установите этот флажок, чтобы быстро открывать Центр Интернета вещей с помощью панели мониторинга.

1. Щелкните **Создать**. Создание Центра Интернета вещей может занять несколько минут. Ход создания отображается на панели **уведомлений**.

   ![Проверка хода создания Центра Интернета вещей на панели уведомлений](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. После создания Центра Интернета вещей щелкните его на панели мониторинга. Запишите значение **имени узла**, которое понадобится позже, а затем щелкните **Политики общего доступа**.

   ![Получение имени узла для Центра Интернета вещей](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. В области **политик общего доступа** выберите политику **iothubowner**, а затем скопируйте и сохраните значение **строки подключения** Центра Интернета вещей. Это значение вам понадобится позже. Дополнительные сведения см. в статье [Управление доступом к Центру Интернета вещей](iot-hub-devguide-security.md).

   ![Получение строки подключения Центра Интернета вещей](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

Вы создали Центр Интернета вещей. Сохраните **имя узла** и **строку подключения**. Эти значения понадобятся вам позже.


### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>Регистрация устройства для Feather HUZZAH ESP8266 в Центре Интернета вещей

Каждый Центр Интернета вещей имеет реестр удостоверений, в котором содержатся сведения об устройствах с разрешением на подключение к Центру Интернета вещей. Перед подключением устройства к Центру Интернета вещей в реестр удостоверений нужно добавить запись об этом устройстве.


В этом разделе вы будете использовать инструмент интерфейса командной строки *iothub explorer*. Используйте этот инструмент, чтобы зарегистрировать устройство для Feather HUZZAH ESP8266 в реестре удостоверений Центра Интернета вещей.



> [!NOTE]
> Для правильной работы iothub explorer требуется Node.js 4.x или более поздняя версия.

Чтобы зарегистрировать устройство для Feather HUZZAH ESP8266, сделайте следующее:

1. [Скачайте](https://nodejs.org/en/download/) и установите последнюю версию Node.js LTS (в том числе npm).
1. Установите iothub-explorer с помощью npm.

   * Для Windows 7 или более поздней версии:

     Запустите командную строку от имени администратора. Установите iothub-explorer, выполнив следующую команду:

     ```bash
     npm install -g iothub-explorer
     ```

   * Для Ubuntu 16.04 или более поздней версии:

     Откройте окно терминала, нажав клавиши CTRL+ALT+T, а затем выполните следующую команду:

     ```bash
     sudo npm install -g iothub-explorer
     ```

   * Для macOS 10.1 или более поздней версии:

     Откройте окно терминала и выполните следующую команду:

     ```bash
     npm install -g iothub-explorer
     ```

3. Войдите в свой Центр Интернета вещей Azure с помощью следующей команды:

   ```bash
   iothub-explorer login [your IoT hub connection string]
   ```

4. Зарегистрируйте новое устройство. В примере ниже `deviceID` является `new-device`. Получите строку подключения, выполнив следующую команду:

   ```bash
   iothub-explorer create new-device --connection-string
   ```

Запишите строку подключения зарегистрированного устройства. Она понадобится позже.


> [!NOTE]
> Чтобы просмотреть строку подключения для зарегистрированных устройств, выполните команду `iothub-explorer list`.


## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Подключение Feather HUZZAH ESP8266 к датчику и компьютеру
В этом разделе рассказывается о том, как подключить датчики к плате. Затем устройство нужно подключить к компьютеру для дальнейшего использования.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Подключение датчика температуры и влажности DHT22 к Feather HUZZAH ESP8266

Чтобы установить подключение, используйте монтажную плату и оптоволоконный кабель с разъемами на обоих концах, как показано ниже. Если у вас нет датчика, пропустите этот раздел, так как вместо него вы можете использовать симулированные данные.

![Справочные материалы по подключению](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


Чтобы подключить выводы датчика, используйте следующие кабели:


| Начало (датчик)           | Конец (плата)           | Цвет кабеля   |
| -----------------------  | ---------------------- | ------------: |
| VDD (вывод 31F)            | 3V (вывод 58H)           | Красный кабель     |
| DATA (вывод 32F)           | GPIO 2 (вывод 46A)       | Синий кабель    |
| GND (вывод 34F)            | GND (вывод 56I)          | Черный кабель   |

Дополнительные сведения см. в статьях о [подключении к датчику Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) и [выводах Adafruit Feather HUZZAH Esp8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).





Теперь работающий датчик должен быть подключен к Feather HUZZAH ESP8266.

![Подключение DHT22 к Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Подключение Feather HUZZAH ESP8266 к компьютеру

Чтобы подключить Feather HUZZAH ESP8266 к своему компьютеру, используйте кабель micro-USB типа A, как показано ниже.

![Подключение Feather Huzzah к компьютеру](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Добавление разрешений для последовательного порта (только в Ubuntu)


При использовании Ubuntu получите разрешения для работы с USB-портом платы Feather HUZZAH ESP8266. Чтобы добавить разрешения для работы с последовательным портом, сделайте следующее:


1. В окне терминала выполните следующие команды:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Вы получите выходные данные одного из следующих типов:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   В выходных данных обратите внимание на параметр `uucp` или `dialout`. Это имя владельца группы USB-порта.

1. Добавьте пользователя в группу, выполнив следующую команду:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` — это имя владельца группы, полученное на предыдущем этапе. `<username>` — это имя пользователя Ubuntu.

1. Чтобы изменения стали видны, выйдите из Ubuntu и войдите снова.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Сбор данных датчиков и их отправка в Центр Интернета вещей

В этом разделе вы развернете и запустите пример приложения на плате Feather HUZZAH ESP8266. Пример приложения включает и отключает светодиодный индикатор на плате Feather HUZZAH ESP8266 и отправляет данные о температуре и влажности, собранные с датчика DHT22, в Центр Интернета вещей.

### <a name="get-the-sample-application-from-github"></a>Получение примера приложения из GitHub

Пример приложения находится на сайте GitHub. Клонируйте репозиторий, содержащий пример приложения с GitHub. Чтобы клонировать пример репозитория, сделайте следующее:

1. Откройте окно командной строки или терминала.
1. Перейдите к папке, в которую необходимо сохранить пример приложения.
1. Выполните следующую команду:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Установите пакет для Feather HUZZAH ESP8266 в Arduino IDE:

1. Откройте папку, где хранится пример приложения.
1. Откройте файл app.ino в папке приложения в Arduino IDE.

   ![Открытие примера приложения в Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. В Arduino IDE щелкните **Файл** > **Настройки**.
1. В диалоговом окне **Настройки** щелкните значок рядом с текстовым полем **Additional Boards Manager URLs** (Дополнительные URL-адреса для менеджера плат).
1. Во всплывающем окне введите приведенный ниже URL-адрес и нажмите кнопку **ОК**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Указание URL-адреса пакета в Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. В диалоговом окне **Настройки** нажмите кнопку **ОК**.
1. Щелкните **Инструменты** > **Платы** > **Менеджер плат**, а затем выполните поиск по esp8266.

   Диспетчер плат показывает, что установлена плата ESP8266 2.2.0 или более поздней версии.

   ![Завершение установки пакета ESP8266](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Щелкните **Инструменты** > **Платы** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Установка необходимых библиотек

1. В Arduino IDE щелкните **Скетч** > **Подключить библиотеку** > **Управлять библиотеками**.
1. Выполните поиск приведенных ниже имен библиотек по очереди. Для каждой найденной библиотеки щелкните **Установить**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>У вас нет датчика DHT22?

Если у вас нет датчика DHT22, пример приложения может смоделировать данные о температуре и влажности. Чтобы настроить пример приложения для использования имитации данных, сделайте следующее:

1. Откройте файл `config.h` в папке `app`.
1. Найдите приведенную ниже строку кода и измените значение `false` на `true`.
   ```c
   define SIMULATED_DATA true
   ```
   ![Настройка примера приложения для использования имитации данных](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Сохраните файл с помощью клавиш `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Развертывание примера приложения для Feather HUZZAH ESP8266

1. В Arduino IDE щелкните **Инструменты** > **Порт**, а затем выберите последовательный порт для Feather HUZZAH ESP8266.
1. Чтобы создать и развернуть пример приложения для Feather HUZZAH ESP8266, щелкните **Скетч** > **Загрузка**.

### <a name="enter-your-credentials"></a>Ввод учетных данных

После успешного завершения загрузки введите учетные данные следующим образом:

1. В Arduino IDE щелкните **Инструменты** > **Монитор последовательного порта**.
1. В окне монитора последовательного порта обратите внимание на два раскрывающихся списка в нижнем правом углу.
1. В раскрывающемся списке слева выберите **No line ending** (Ничего не добавлять к отправляемой строке).
1. В раскрывающемся списке справа выберите **115200 baud** (115200 бод).
1. При появлении запроса в поле ввода, расположенном в верхней части окна монитора последовательного порта, введете приведенные ниже сведения, а затем нажмите кнопку **Отправить**.
   * Идентификатор SSID для подключения Wi-Fi.
   * Пароль Wi-Fi.
   * Строка подключения к устройству.

> [!Note]
> Учетные данные хранятся в EEPROM платы Feather HUZZAH ESP8266. При нажатии кнопки сброса на плате Feather HUZZAH ESP8266 в примере приложения отобразится сообщение с запросом на удаление информации. Введите `Y` для удаления данных. Вам будет предложено предоставить их еще раз.

### <a name="verify-the-sample-application-is-running-successfully"></a>Проверка работоспособности примера приложения

Если в окне монитора последовательного порта отобразились приведенные ниже выходные данные и на плате Feather HUZZAH ESP8266 мигает светодиодный индикатор, значит пример приложения запущен успешно.

![Конечные выходные данные в Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату Feather HUZZAH ESP8266 к Центру Интернета вещей и отправили в него собранные данные датчика. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


