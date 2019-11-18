---
title: Funkcje szablonu — wdrożenie
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 17caf78fb77e330685bb45ab03aaeed611900ba0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149638"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funkcje wdrażania dla Azure Resource Manager szablonów 

Menedżer zasobów udostępnia następujące funkcje do uzyskiwania wartości z sekcji szablonu i wartości związanych ze wdrożeniem:

* [mieszczeniu](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Aby uzyskać wartości z zasobów, grup zasobów lub subskrypcji, zobacz temat [funkcje zasobów](resource-group-template-functions-resource.md).

<a id="deployment" />

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

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Zwraca wartość parametru. Określona nazwa parametru musi być zdefiniowana w sekcji Parameters szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringOutput | Ciąg | Opcja 1 |
| intOutput | Int | 1 |
| objectOutput | Obiekt | {"jeden": "a", "dwa": "b"} |
| arrayOutput | Tablica | [1, 2, 3] |
| crossOutput | Ciąg | Opcja 1 |

Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Parametry w szablonie Azure Resource Manager](template-parameters.md).

<a id="variables" />

## <a name="variables"></a>Modyfikacj
`variables(variableName)`

Zwraca wartość zmiennej. Określona nazwa zmiennej musi być zdefiniowana w sekcji zmiennych szablonu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| exampleOutput1 | Ciąg | NazwaMojejZmiennej |
| exampleOutput2 | Tablica | [1, 2, 3, 4] |
| exampleOutput3 | Ciąg | NazwaMojejZmiennej |
| exampleOutput4 |  Obiekt | {"Property1": "wartość1", "Property2": "wartość2"} |

Aby uzyskać więcej informacji o używaniu zmiennych, zobacz [zmienne w szablonie Azure Resource Manager](template-variables.md).

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić kilka szablonów, zobacz [Używanie połączonych szablonów z Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

