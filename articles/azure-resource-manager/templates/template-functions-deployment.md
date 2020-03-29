---
title: Funkcje szablonu — wdrażanie
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 86a1d3d7e05fedacd7a3c044ecab241ca9d059c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156331"
---
# <a name="deployment-functions-for-arm-templates"></a>Funkcje wdrażania szablonów ARM 

Menedżer zasobów udostępnia następujące funkcje uzyskiwania wartości związanych z bieżącym wdrożeniem szablonu usługi Azure Resource Manager (ARM):

* [Wdrażania](#deployment)
* [Środowiska](#environment)
* [Parametry](#parameters)
* [Zmiennych](#variables)

Aby uzyskać wartości z zasobów, grup zasobów lub subskrypcji, zobacz [Funkcje zasobów](template-functions-resource.md).

## <a name="deployment"></a>wdrożenie

`deployment()`

Zwraca informacje o bieżącej operacji wdrażania.

### <a name="return-value"></a>Wartość zwracana

Ta funkcja zwraca obiekt, który jest przekazywany podczas wdrażania. Właściwości w zwróconym obiekcie różnią się w zależności od tego, czy obiekt wdrożenia jest przekazywany jako łącze, czy jako obiekt w wierszu. Gdy obiekt wdrażania jest przekazywany w wierszu, na przykład podczas korzystania z **parametru -TemplateFile** w programie Azure PowerShell do wskażenia pliku lokalnego, zwrócony obiekt ma następujący format:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Gdy obiekt jest przekazywany jako łącze, na przykład podczas używania parametru **-TemplateUri** do wskażenia obiektu zdalnego, obiekt jest zwracany w następującym formacie: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Podczas [wdrażania w ramach subskrypcji platformy Azure](deploy-to-subscription.md)zamiast grupy zasobów `location` obiekt zwracany zawiera właściwość. Właściwość lokalizacji jest uwzględniona podczas wdrażania szablonu lokalnego lub zewnętrznego.

### <a name="remarks"></a>Uwagi

Za pomocą metody deployment() można utworzyć łącze do innego szablonu opartego na identyfikatorze URI szablonu nadrzędnego.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Jeśli ponownie wdrożysz szablon z historii wdrażania w portalu, szablon zostanie wdrożony jako plik lokalny. Właściwość `templateLink` nie jest zwracana w funkcji wdrażania. Jeśli szablon opiera `templateLink` się na konstruowaniu łącza do innego szablonu, nie używaj portalu do ponownego rozmieszczenia. Zamiast tego należy użyć poleceń użytych do oryginalnego wdrożenia szablonu.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) zwraca obiekt wdrożenia:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

W poprzednim przykładzie zwraca następujący obiekt:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Aby uzyskać szablon na poziomie subskrypcji, który korzysta z funkcji wdrażania, zobacz [funkcję wdrażania subskrypcji](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Jest on wdrażany za `az deployment create` `New-AzDeployment` pomocą albo polecenia.

## <a name="environment"></a>environment

`environment()`

Zwraca informacje o środowisku platformy Azure używanym do wdrażania.

### <a name="return-value"></a>Wartość zwracana

Ta funkcja zwraca właściwości dla bieżącego środowiska platformy Azure. W poniższym przykładzie przedstawiono właściwości globalnej platformy Azure. Suwerenne chmury mogą zwracać nieco inne właściwości.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Przykład

Poniższy przykładowy szablon zwraca obiekt środowiska.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

W poprzednim przykładzie zwraca następujący obiekt po wdrożeniu na globalnej platformie Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Zwraca wartość parametru. Określona nazwa parametru musi być zdefiniowana w sekcji parametrów szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Parametername |Tak |ciąg |Nazwa parametru do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonego parametru.

### <a name="remarks"></a>Uwagi

Zazwyczaj parametry służą do ustawiania wartości zasobów. Poniższy przykład ustawia nazwę witryny sieci web na wartość parametru przekazywany podczas wdrażania.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) przedstawia uproszczone użycie funkcji parametrów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ciągOutput | Ciąg | wariant 1 |
| intOutput (Nieuprzejmych) | int | 1 |
| ObjectOutput | Obiekt | {"jeden": "a", "dwa": "b"} |
| arrayOutput | Tablica | [1, 2, 3] |
| crossOutput (pl/ | Ciąg | wariant 1 |

Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Parametry w szablonie usługi Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>Zmiennych

`variables(variableName)`

Zwraca wartość zmiennej. Określona nazwa zmiennej musi być zdefiniowana w sekcji zmiennych szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Variablename |Tak |Ciąg |Nazwa zmiennej do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonej zmiennej.

### <a name="remarks"></a>Uwagi

Zazwyczaj zmienne są używane do uproszczenia szablonu przez konstruowanie złożonych wartości tylko raz. Poniższy przykład tworzy unikatową nazwę konta magazynu.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) zwraca różne wartości zmiennych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| przykładOutput1 | Ciąg | myVariable (Możliwe do wytłk |
| exampleOutput2 | Tablica | [1, 2, 3, 4] |
| exampleOutput3 | Ciąg | myVariable (Możliwe do wytłk |
| przykładOutput4 |  Obiekt | {"property1": "value1", "property2": "value2"} |

Aby uzyskać więcej informacji na temat używania zmiennych, zobacz [Zmienne w szablonie Usługi Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać opis sekcji w szablonie usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby scalić kilka szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby zobaczyć, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

