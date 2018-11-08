---
title: Funkcje szablonu usługi Resource Manager platformy Azure — zasoby | Dokumentacja firmy Microsoft
description: Opisuje funkcje, aby użyć w szablonie usługi Azure Resource Manager można pobrać wartości dotyczące zasobów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 6da2f7792df564ea3a41df37ab9b00574a205e5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219549"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funkcje zasobów dla szablonów usługi Azure Resource Manager

Usługa Resource Manager zapewnia następujące funkcje w celu uzyskania wartości zasobu:

* [listAccountSas](#list)
* [klucze list](#listkeys)
* [listSecrets](#list)
* [Lista *](#list)
* [dostawcy](#providers)
* [Odwołanie](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [Subskrypcja](#subscription)

Aby uzyskać wartości parametrów, zmienne lub bieżącego wdrożenia, zobacz [funkcji wartość wdrożeniowych](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listaccountsas-listkeys-listsecrets-and-list"></a>listAccountSas listKeys, listSecrets i listy *
`listAccountSas(resourceName or resourceIdentifier, apiVersion, functionValues)`

`listKeys(resourceName or resourceIdentifier, apiVersion)`

`listSecrets(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Zwraca wartości dla dowolnego typu zasobu, który obsługuje operacja wygenerowania listy. To najbardziej typowego użycia `listKeys` i `listSecrets`. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Yes |ciąg |Unikatowy identyfikator dla zasobu. |
| apiVersion |Yes |ciąg |Wersja interfejsu API stanu środowiska uruchomieniowego zasobu. Zazwyczaj w formacie **rrrr mm-dd**. |
| functionValues |Nie |obiekt | Obiekt, który zawiera wartości dla funkcji. Podaj tylko ten obiekt funkcji, które obsługują odbieranie obiekt o wartości parametrów, takich jak **listAccountSas** na koncie magazynu. | 

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt z listKeys ma następujący format:

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

Inne funkcje listy mają różne formaty zwrotu. Aby wyświetlić format funkcji, dołączyć sekcję danych wyjściowych przedstawiony przykładowy szablon. 

### <a name="remarks"></a>Uwagi

Każdej operacji, która rozpoczyna się od **listy** mogą być używane jako funkcję w szablonie. Dostępne operacje obejmują nie tylko klucze listy, ale również operacje, takie jak `list`, `listAdminKeys`, i `listStatus`. [Sygnatury dostępu Współdzielonego listy konta](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) operacja wymaga parametrów treści żądania, takie jak *signedExpiry*. Aby użyć tej funkcji w szablonie, Udostępnij obiekt treści wartości parametrów.

Aby ustalić, typów zasobów, które mają operacja listy, dostępne są następujące opcje:

* Widok [operacji interfejsu API REST](/rest/api/) dla dostawcy zasobów i wygląd na potrzeby operacji listy. Na przykład konta magazynu mają [operacji listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Użyj [polecenia Get](/powershell/module/azurerm.resources/get-azurermprovideroperation) polecenia cmdlet programu PowerShell. Poniższy przykład pobiera wszystkie operacje dotyczące list dla kont magazynu:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Użyj następującego polecenia wiersza polecenia platformy Azure, aby filtrować Wyświetl listę operacji:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Określ zasób, używając nazwy zasobu lub [funkcja resourceId](#resourceid). Podczas korzystania z tej funkcji, w tym samym szablonie, który wdraża przywoływany zasób, należy użyć nazwy zasobu.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) przedstawia sposób zwrócenia klucze podstawowe i pomocnicze z konta magazynu w sekcji danych wyjściowych. Zwraca token sygnatury dostępu Współdzielonego dla konta magazynu. Aby uzyskać token, przekazuje obiekt listAccountSas funkcji. W tym przykładzie jest przeznaczona do korzystania z funkcji listy. Zazwyczaj można będzie użyć tokenu sygnatury dostępu Współdzielonego w wartości zasobów zamiast zwracanie go jako wartość wyjściową. Wartości wyjściowe są zapisywane w historii wdrożenia i nie są bezpieczne. Należy określić czas wygaśnięcia w przyszłości do pomyślnego wdrożenia.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
``` 

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storagename=<your-storage-account>
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storagename <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>dostawcy
`providers(providerNamespace, [resourceType])`

Zwraca informacje o dostawcy zasobów i jego obsługiwane typy zasobów. Jeśli nie podasz zasobu o typie, funkcja zwraca obsługiwane typy dla dostawcy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Yes |ciąg |Namespace dostawcy |
| Typ zasobu |Nie |ciąg |Typ zasobu w ramach określonego obszaru nazw. |

### <a name="return-value"></a>Wartość zwracana

Każdego obsługiwanego typu, jest zwracany w następującym formacie: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Kolejność zwracanych wartości tablicy nie jest gwarantowana.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) pokazuje, jak korzystać z funkcji dostawcy:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Aby uzyskać **Microsoft.Web** dostawcy zasobów i **witryn** typ zasobu w poprzednim przykładzie zwracany obiekt w następującym formacie:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>Odwołanie
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Zwraca obiekt reprezentujący stan czasu wykonywania zasobu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Yes |ciąg |Nazwa lub identyfikator zasobu. |
| apiVersion |Nie |ciąg |Wersja interfejsu API określonego zasobu. Ten parametr należy uwzględnić, jeśli zasób nie jest zainicjowana obsługa administracyjna w obrębie tego samego szablonu. Zazwyczaj w formacie **rrrr mm-dd**. |
| "Full" |Nie |ciąg |Wartość określająca, czy należy zwrócić obiekt wszystkich zasobów. Jeśli nie określisz `'Full'`, zwracany jest tylko obiekt do właściwości zasobu. Pełny obiekt zawiera wartości, takie jak identyfikator zasobu i lokalizacji. |

### <a name="return-value"></a>Wartość zwracana

Każdy typ zasobu zwraca różne właściwości odwołanie funkcji. Funkcja nie zwraca jednego, wstępnie zdefiniowany format. Ponadto zwrócona wartość różni się zależnie od tego, czy określony obiekt pełne. Aby wyświetlić właściwości dla typu zasobu, należy przywrócić obiektu w sekcji danych wyjściowych, jak pokazano w przykładzie.

### <a name="remarks"></a>Uwagi

Funkcja odwołanie pochodzi wartość ze stanu środowiska uruchomieniowego i nie można używać w sekcji zmiennych. Może służyć w sekcji danych wyjściowych szablonu lub [połączony szablon](resource-group-linked-templates.md#link-or-nest-a-template). Nie można używać w sekcji danych wyjściowych [zagnieżdżonych szablonów](resource-group-linked-templates.md#link-or-nest-a-template). Aby zwrócić wartości dla zasobów wdrożonych w zagnieżdżonych szablonów, należy przekonwertować zagnieżdżony szablon do dołączonego szablonu. 

Za pomocą funkcji odwołania, niejawnie Deklarujesz, jeden zasób jest zależny od innego zasobu, jeśli przywoływany zasób jest obsługiwana w ramach tego samego szablonu i odwołania do zasobu według jego nazwy (a nie identyfikator zasobu). Nie musisz również użyć właściwości dependsOn. Funkcja nie jest obliczane, dopóki nie zakończy się przywoływany zasób wdrożenia.

Aby wyświetlić nazwy właściwości i wartości dla typu zasobu, należy utworzyć szablon, który zwraca obiekt, w sekcji danych wyjściowych. W przypadku istniejącego zasobu tego typu szablonu zwraca obiekt bez wdrażania wszystkie nowe zasoby. 

Zazwyczaj można użyć **odwołania** funkcja zwraca określoną wartość z obiektu, takie jak identyfikator URI punktu końcowego obiektu blob lub w pełni kwalifikowaną nazwę domeny.

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

Użyj `'Full'` potrzebne wartości zasobów, które nie są częścią schematu właściwości. Na przykład aby ustawić zasady dostępu magazynu kluczy, pobieranie właściwości tożsamości dla maszyny wirtualnej.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

Aby uzyskać kompletny przykład powyższy szablon, zobacz [Windows do usługi Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Podobny przykład jest dostępny dla [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) wdraża zasobu, a następnie odwołuje się do tego zasobu.

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
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Powyższy przykład zwraca dwa obiekty. Obiekt właściwości znajduje się w następującym formacie:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Pełny obiekt znajduje się w następującym formacie:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odwołuje się do konta magazynu, który nie jest wdrożony w tym szablonie. Konto magazynu już istnieje w tej samej grupie zasobów.

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

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageAccountName=<your-storage-account>
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Zwraca obiekt, który reprezentuje bieżącą grupę zasobów. 

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt jest w następującym formacie:

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

### <a name="remarks"></a>Uwagi

Typowym zastosowaniem funkcji resourceGroup jest do tworzenia zasobów w tej samej lokalizacji co grupa zasobów. W poniższym przykładzie użyto lokalizacji grupy zasobów, aby przypisać lokalizacji dla witryny sieci web.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) zwraca właściwości grupy zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Powyższy przykład zwraca obiekt, w następującym formacie:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Zwraca unikatowy identyfikator zasobu. Aby użyć tej funkcji, jeśli nazwa zasobu jest niejednoznaczny lub nie jest aprowizowany w ramach tego samego szablonu. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |ciąg (format identyfikatora GUID w) |Wartością domyślną jest bieżąca subskrypcja. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej subskrypcji. |
| resourceGroupName |Nie |ciąg |Wartość domyślna to bieżącej grupie zasobów. Należy określić tę wartość, gdy jest potrzebne do pobierania zasobów w innej grupie zasobów. |
| Typ zasobu |Yes |ciąg |Typ zasobu, włącznie z przestrzenią nazw dostawcy zasobów. |
| resourceName1 |Yes |ciąg |Nazwa zasobu. |
| resourceName2 |Nie |ciąg |Następny zasobu Nazwa segment Jeśli zasób jest zagnieżdżony. |

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Wartości parametrów, które określisz, zależą od tego, czy zasób jest w tej samej subskrypcji i grupie zasobów co bieżącego wdrożenia.

Aby uzyskać identyfikator zasobu dla konta magazynu w tej samej subskrypcji i grupie zasobów, użyj:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla konta magazynu w tej samej subskrypcji, ale inną grupę zasobów, użyj:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla konta magazynu w innej subskrypcji i grupie zasobów, użyj:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Aby uzyskać identyfikator zasobu dla bazy danych w innej grupie zasobów, należy użyć:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Często należy użyć tej funkcji, korzystając z konta magazynu lub sieci wirtualnej w grupie zasobów alternatywne. Poniższy przykład pokazuje, jak łatwo można używać zasobów z grupy zasobów zewnętrznych:

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

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) zwraca identyfikator zasobu dla konta magazynu w grupie zasobów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| sameRGOutput | Ciąg | /Subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Ciąg | /Subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Ciąg | /Subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/Providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>subskrypcja
`subscription()`

Zwraca szczegółowe informacje o subskrypcji dla bieżącego wdrożenia. 

### <a name="return-value"></a>Wartość zwracana

Funkcja zwraca następujący format:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) przedstawia funkcję subskrypcji o nazwie w sekcję danych wyjściowych. 

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

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

