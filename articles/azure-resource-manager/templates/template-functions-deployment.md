---
title: Funkcje szablonu — wdrożenie
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: b241aaf43ee3204c9960d0099ce3c61d4c1a80ee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274270"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funkcje wdrażania dla Azure Resource Manager szablonów 

Menedżer zasobów udostępnia następujące funkcje do uzyskiwania wartości związanych z bieżącym wdrożeniem:

* [mieszczeniu](#deployment)
* [naturalne](#environment)
* [parameters](#parameters)
* [modyfikacj](#variables)

Aby uzyskać wartości z zasobów, grup zasobów lub subskrypcji, zobacz temat [funkcje zasobów](template-functions-resource.md).

## <a name="deployment"></a>wdrożenie

`deployment()`

Zwraca informacje o bieżącej operacji wdrażania.

### <a name="return-value"></a>Wartość zwracana

Ta funkcja zwraca obiekt, który jest przesyłany podczas wdrażania. Właściwości w zwracanym obiekcie różnią się w zależności od tego, czy obiekt wdrożenia jest przekazana jako link, czy jako obiekt w wierszu. Gdy obiekt wdrożenia jest przesyłany w trybie online, na przykład w przypadku użycia parametru **-TemplateFile** w Azure PowerShell, aby wskazywał na plik lokalny, zwracany obiekt ma następujący format:

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

Gdy obiekt jest przenoszona jako link, na przykład w przypadku użycia parametru **-TemplateUri** w celu wskazywania obiektu zdalnego, obiekt jest zwracany w następującym formacie: 

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

Po [wdrożeniu do subskrypcji platformy Azure](deploy-to-subscription.md), a nie w grupie zasobów, obiekt zwracany zawiera właściwość `location`. Właściwość Location jest uwzględniana podczas wdrażania szablonu lokalnego lub zewnętrznego.

### <a name="remarks"></a>Uwagi

Możesz użyć wdrożenia (), aby połączyć się z innym szablonem na podstawie identyfikatora URI szablonu nadrzędnego.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Po ponownym wdrożeniu szablonu z historii wdrożenia w portalu szablon zostanie wdrożony jako plik lokalny. Właściwość `templateLink` nie została zwrócona w funkcji wdrożenia. Jeśli szablon polega na `templateLink` konstruowania linku do innego szablonu, nie należy używać portalu do ponownego wdrożenia. Zamiast tego należy użyć poleceń użytych do pierwotnego wdrożenia szablonu.

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

Poprzedni przykład zwraca następujący obiekt:

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

Aby zapoznać się z szablonem na poziomie subskrypcji korzystającym z funkcji wdrażania, zobacz [Funkcja wdrażania subskrypcji](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Jest ona wdrażana za pomocą poleceń `az deployment create` lub `New-AzDeployment`.

## <a name="environment"></a>environment

`environment()`

Zwraca informacje o środowisku platformy Azure używanym do wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Ta funkcja zwraca właściwości dla bieżącego środowiska platformy Azure. W poniższym przykładzie przedstawiono właściwości globalne platformy Azure. Suwerenne chmury mogą zwracać nieco inne właściwości.

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

Poprzedni przykład zwraca następujący obiekt po wdrożeniu na globalnym platformie Azure:

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

Zwraca wartość parametru. Określona nazwa parametru musi być zdefiniowana w sekcji Parameters szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |ciąg |Nazwa parametru do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonego parametru.

### <a name="remarks"></a>Uwagi

Zazwyczaj należy używać parametrów do ustawiania wartości zasobów. W poniższym przykładzie ustawiono nazwę witryny sieci Web do wartości parametru przesłanej podczas wdrażania.

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

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) pokazuje uproszczone użycie funkcji Parameters.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| stringOutput | Ciąg | Opcja 1 |
| intOutput | Int | 1 |
| objectOutput | Obiekt | {"jeden": "a", "dwa": "b"} |
| arrayOutput | Tablica | [1, 2, 3] |
| crossOutput | Ciąg | Opcja 1 |

Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Parametry w szablonie Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>zmienne

`variables(variableName)`

Zwraca wartość zmiennej. Określona nazwa zmiennej musi być zdefiniowana w sekcji zmiennych szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| variableName |Yes |Ciąg |Nazwa zmiennej do zwrócenia. |

### <a name="return-value"></a>Wartość zwracana

Wartość określonej zmiennej.

### <a name="remarks"></a>Uwagi

Zwykle zmienne służą do uproszczenia szablonu przez konstruowanie złożonych wartości tylko raz. Poniższy przykład tworzy unikatową nazwę konta magazynu.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| exampleOutput1 | Ciąg | NazwaMojejZmiennej |
| exampleOutput2 | Tablica | [1, 2, 3, 4] |
| exampleOutput3 | Ciąg | NazwaMojejZmiennej |
| exampleOutput4 |  Obiekt | {"Property1": "wartość1", "Property2": "wartość2"} |

Aby uzyskać więcej informacji o używaniu zmiennych, zobacz [zmienne w szablonie Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Następne kroki
* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [tworzenie Azure Resource Manager szablonów](template-syntax.md).
* Aby scalić kilka szablonów, zobacz [Używanie połączonych szablonów z Azure Resource Manager](linked-templates.md).
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w Azure Resource Manager](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

