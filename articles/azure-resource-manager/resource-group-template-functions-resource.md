---
title: "Функции шаблонов Azure Resource Manager для работы с ресурсами | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне Azure Resource Manager для получения значений ресурсов."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 66c71906614e5d0c8e8531271444fc59a5cb779f
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Функции для работы с ресурсами в шаблонах Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для получения значений ресурсов:

* [listKeys и list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

Получение значений параметров, переменных или текущего развертывания описано в разделе [Функции для параметров развертывания](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys и list{Value}
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Возвращает значения для любого типа ресурса, который поддерживает операцию list. Наиболее распространенным вариантом применения является `listKeys`. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Да |string |Уникальный идентификатор ресурса. |
| версия_API |Да |string |Версия API для состояния среды выполнения ресурса. Как правило, указывается в формате **гггг-мм-дд**. |

### <a name="remarks"></a>Примечания

Любая операция, которая начинается с **list**, может быть использована в шаблоне как функция. Это относится не только к listKeys, но и к таким операциям, как `list`, `listAdminKeys` и `listStatus`. Чтобы определить, какие типы ресурсов поддерживают операцию list, можно использовать следующие варианты:

* Просмотрите [операции REST API](/rest/api/) для поставщика ресурсов и найдите операции list. Например, в учетных записях хранения есть [операция listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Воспользуйтесь командлетом PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). В следующем примере извлекаются все операции list для учетных записей хранения:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Используйте следующую команду Azure CLI и служебную программу JSON [jq](http://stedolan.github.io/jq/download/), чтобы отфильтровать только операции list:

  ```azurecli
  azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
  ```

Укажите ресурс с помощью [функции resourceId](#resourceid) или формата `{providerNamespace}/{resourceType}/{resourceName}`.

### <a name="examples"></a>Примеры

В следующем примере показано, как получить в разделе выходных данных первичный и вторичный ключи из учетной записи хранения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountId": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "storageKeysOutput": {
            "value": "[listKeys(parameters('storageAccountId'), '2016-01-01')]",
            "type" : "object"
        }
    }
}
``` 

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый функцией listKeys объект имеет следующий формат:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Другие функции list возвращают данные в других форматах. Чтобы просмотреть формат функции, включите ее в раздел outputs, как показано в примере шаблона. 

<a id="providers" />

## <a name="providers"></a>providers
`providers(providerNamespace, [resourceType])`

Возвращает сведения о поставщике ресурсов и поддерживаемых типах ресурсов. Если тип ресурса не указан, функция возвращает все типы, поддерживаемые для поставщика ресурсов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| пространство_имен_поставщика |Да |string |Пространство имен поставщика. |
| тип_ресурса |Нет |string |Тип ресурса в указанном пространстве имен. |

### <a name="examples"></a>Примеры

Следующий пример показывает, как использовать функцию provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Все поддерживаемые типы возвращаются в следующем формате: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Упорядочение массива возвращаемых значений не гарантируется.

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion])`

Возвращает объект, представляющий состояние среды выполнения ресурса.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Да |string |Имя или уникальный идентификатор ресурса. |
| версия_API |Нет |string |Версия API для указанного ресурса. Если ресурс не предоставляется в рамках того же шаблона, необходимо включить этот параметр. Как правило, указывается в формате **гггг-мм-дд**. |

### <a name="remarks"></a>Примечания

Функция reference получает свое значение из состояния среды выполнения, и поэтому ее невозможно использовать в разделе переменных. Она может использоваться в разделе выходных данных шаблона. 

С помощью функции reference вы прямо объявляете, что один ресурс зависит от другого, если ресурс, на который указывает ссылка, предоставляется в том же шаблоне. При этом свойство dependsOn использовать не нужно. Расчет функции выполняется только после развертывания ресурса, на который указывает ссылка.

Чтобы просмотреть имена и значения свойств для типа ресурса, создайте в разделе outputs шаблон, который возвращает объект. Если ресурс этого типа уже существует, то шаблон возвращает объект, не развертывая новых ресурсов. 

### <a name="examples"></a>Примеры

Следующий пример ссылается на учетную запись хранения, которая не развертывается в этом шаблоне, но существует в той же группе ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

Также можно развернуть ресурс и сослаться на него в одном шаблоне.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      }
    }
}
``` 

Обычно функция reference используется, чтобы получить определенное значение из объекта, например универсальный код ресурса (URI) конечной точки большого двоичного объекта или полное доменное имя.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Каждый тип ресурса возвращает для функции reference разные свойства. Функция не возвращает данные в едином предварительно заданном формате. Чтобы просмотреть свойства для типа ресурса, возвратите объект в разделе outputs, как показано в примере.

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Возвращает объект, который представляет текущую группу ресурсов. 

### <a name="examples"></a>Примеры

Следующий шаблон возвращает свойства группы ресурсов.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Как правило, функция resourceGroup используется для создания ресурсов в одном расположении с группой ресурсов. В следующем примере расположение группы ресурсов используется для назначения расположения веб-сайту.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Возвращает уникальный идентификатор ресурса. Используйте эту функцию в том случае, когда имя ресурса является неоднозначным или не было предоставлено в пределах того же шаблона. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| subscriptionId |Нет |строка (в формате GUID) |Значение по умолчанию — текущая подписка. Укажите это значение, если нужно получить ресурс из другой подписки. |
| имя_группы_ресурсов |Нет |string |Значение по умолчанию — текущая группа ресурсов. Укажите это значение, если нужно получить ресурс из другой группы ресурсов. |
| тип_ресурса |Да |string |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Да |string |Имя ресурса. |
| имя_ресурса2 |Нет |string |Имя следующего ресурса, если ресурс является вложенным. |

### <a name="examples"></a>Примеры

Следующий пример возвращает идентификатор ресурса для учетной записи хранения в группе ресурсов:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceIdOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        }
    }
}
```

В следующем примере показано, как получить идентификаторы ресурсов для веб-сайта в другой группе ресурсов и базы данных в другой группе ресурсов:

```json
[resourceId('otherResourceGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

Эта функция часто необходима при использовании учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. Учетная запись хранения и виртуальная сеть могут использоваться несколькими группами ресурсов, поэтому их не следует удалять при удалении одной группы ресурсов. В следующем примере показано, как ресурс из внешней группы ресурсов можно легко использовать:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="return-value"></a>Возвращаемое значение

Идентификатор возвращается в следующем формате:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

<a id="subscription" />

## <a name="subscription"></a>Подписка
`subscription()`

Возвращает сведения о подписке для текущего развертывания. 

### <a name="examples"></a>Примеры

В следующем примере показана функция subscription, вызываемая в разделе выходных данных. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Функция возвращает значение в следующем формате:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


